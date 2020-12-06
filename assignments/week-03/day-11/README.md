# Database Migration & Security

Monday 07.12.2020

## Objectives
Today we want to add database migration to our server, so that we can upgrade or downgrade our database schema as needed.
We will also refactor our database code so that it's using an ORM to improve code readability and security.
- [ ] See how our current server is vulnerable to SQL injection attacks.  
- [ ] Setup flask-sqlalchemy as our ORM.
- [ ] Generate migrations for our database.
- [ ] Update the migrations for our database.
- [ ] (Bonus) Setup security scanning for our repository.

## Part 1 - SQL injection

SQL injections are one of the most common security vulnerabilities in applications in the world.
It's a code injection technique where malicious code is inserted into an entry field for execution.

If you are using the code we gave you for the server in week 2, your code is vulnerable to this type
of attack:

`1337hack.py`
~~~python
import requests
import sys

offset=sys.argv[1]

response = requests.get("http://connect-four-server-production.dr3amt3am.hgopteam.com/get_game", params={
    "gameId": f"' OR TRUE OFFSET {offset};--",
    "playerId": ""
})

print(response.json())
~~~

```bash
python 1337hack.py [OFFSET]
```

Using the code above an attacker can scan the games in the TA's database and get hold of gameId tokens and playerId tokens
and using those the attack is capable impersonating users.

In your answers for the day, write in:


- All the SQL statements that get executed on the server when you execute the test above. You can figure this out by looking at
how the request data (gameId and playerId) are used by the server and sent to the database.

Example:

~~~SQL
SELECT id, gameId, number
FROM Player
WHERE gameId = 'opzietykhc5cibtiqejifsn14j9awqsr';
~~~

The answers markdown should be well formatted (look nice when rendered).


## Part 2 - Database ORM

Now we will setup `Flask-SQLAlchemy` and `Flask-Migrate` for our server, add the following to your `requirements.txt`:

~~~
alembic==1.4.3
Flask-Migrate==2.5.3
Flask-Script==2.0.6
Flask-SQLAlchemy==2.4.4
~~~

SQLAlchemy uses connection strings to connect with the database, add this to your `connect4/config.py`:

~~~python
SQLALCHEMY_DATABASE_URI = f"postgresql://{DATABASE_USERNAME}:{DATABASE_PASSWORD}@{DATABASE_HOST}:{DATABASE_PORT}/{DATABASE_NAME}"
~~~

Now remove the `Database Entities` from `connect4/models.py`.

Now we will recreate them using SQLAlchemy in `connect4/app.py`

~~~python
from connect4 import app_logic
from connect4 import config
from connect4 import exceptions
from flask import Flask, request
from flask_cors import CORS # type: ignore
from flask_migrate import Migrate, MigrateCommand # type: ignore
from flask_script import Manager # type: ignore
from flask_sqlalchemy import SQLAlchemy # type: ignore
from typing import Any, Callable, Optional, Tuple

app = Flask(__name__)
app.config.from_object(config)
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
CORS(app)

migrate = Migrate(app, db)
manager = Manager(app)
manager.add_command('db', MigrateCommand)

class GameEntity(db.Model): # type: ignore
    __tablename__ = 'game'

    gameId = db.Column(db.String(32), primary_key=True)
    active = db.Column(db.Boolean, nullable=False)
    winner = db.Column(db.Integer, nullable=True)
    activePlayer = db.Column(db.Integer, nullable=False)
    board = db.Column(db.String(42), nullable=False)

    def __init__(
        self,
        gameId: str,
        active: bool,
        winner: Optional[int],
        activePlayer: int,
        board: str,
    ) -> None:
        self.gameId = gameId
        self.active = active
        self.winner = winner
        self.activePlayer = activePlayer
        self.board = board


class PlayerEntity(db.Model): # type: ignore
    __tablename__ = 'player'

    playerId = db.Column(db.String(32), primary_key=True)
    gameId = db.Column(db.String(32), db.ForeignKey('game.gameId'), nullable=False)
    number = db.Column(db.Integer, nullable=False)

    __table_args__ = (db.UniqueConstraint('gameId', 'number', name='_game_number_constraint'),)

    def __init__(self, playerId: str, gameId: str, number: int) -> None:
        self.playerId = playerId
        self.gameId = gameId
        self.number = number

def call_wrapper(action: Callable[[], Tuple[Any, int]]) -> Tuple[Any, int]:
    try:
        return action()
    except exceptions.ApiException as ex:
        return {
            "error": ex.message,
        }, ex.status_code
    except Exception:
        return {
            "error": "Internal Server Error",
        }, 500


@app.route("/", methods=["GET"])
def index() -> Tuple[str, int]:
    return call_wrapper(
        lambda: app_logic.index()
    )


@app.route("/status", methods=["GET"])
def status() -> Tuple[str, int]:
    return call_wrapper(
        lambda: app_logic.status()
    )


@app.route("/create_game", methods=["POST"])
def create_game() -> Tuple[dict, int]:
    return call_wrapper(
        lambda: app_logic.create_game(request.json)
    )

@app.route("/join_game", methods=["POST"])
def join_game() -> Tuple[dict, int]:
    return call_wrapper(
        lambda: app_logic.join_game(request.json)
    )

@app.route("/get_game", methods=["GET"])
def get_game() -> Tuple[dict, int]:
    gameId = request.args.get("gameId", "")
    playerId = request.args.get("playerId", "")
    return call_wrapper(
        lambda: app_logic.get_game(gameId, playerId)
    )


@app.route("/make_move", methods=["POST"])
def make_move() -> Tuple[dict, int]:
    return call_wrapper(
        lambda: app_logic.make_move(request.json)
    )


if __name__ == "__main__":
    manager.run()
~~~

Now we refactor the `connect4/database.py`:

~~~python
from connect4.app import db, GameEntity, PlayerEntity
from typing import List, Optional

def create_game(
    gameId: str, active: bool, winner: Optional[int], activePlayer: int, board: str
) -> GameEntity:
    game = GameEntity(
        gameId=gameId,
        active=active,
        winner=winner,
        activePlayer=activePlayer,
        board=board
    )
    db.session.add(game)
    db.session.commit()
    return game

def add_player_to_game(playerId: str, gameId: str, number: int) -> None:
    # TODO


def get_game(gameId: str) -> Optional[GameEntity]:
    return GameEntity.query.get(gameId)


def get_players(gameId: str) -> List[PlayerEntity]:
    return PlayerEntity.query.filter_by(gameId=gameId).all()


def get_player(playerId: str) -> Optional[PlayerEntity]:
    # TODO


def update_game(
    gameId: str, active: bool, winner: Optional[int], activePlayer: int, board: str
) -> None:
    game = GameEntity.query.get(gameId)
    game.active = active
    game.winner = winner,
    game.activePlayer = activePlayer
    game.board = board
    db.session.commit()
~~~

Notice how much more readable the queries are in SQLAlchemy and we don't have to manage raw SQL queries.
SQLAlchemy also escapes the input fields, preventing SQL injection vulnerabilities.

Now lets generate our migrations, we start by creating a local database instance for the generation to
use.

~~~bash
docker run -p 5432:5432 -e POSTGRES_USERNAME=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=connect-four --name database postgres:12
~~~

~~~bash
PYTHONPATH=. python connect4/app.py db init
export DATABASE_USERNAME=postgres
export DATABASE_PASSWORD=postgres
export DATABASE_NAME=connect-four
export DATABASE_HOST=localhost
export DATABASE_PORT=5432
PYTHONPATH=. python connect4/app.py db migrate -m "initial migration"
~~~

You should now have a `migrations` folder in the root of your repository.

You can remove the database instance you just created.

Now we need to update our `Dockerfile` to include our migrations as well as an entry script that runs the migrations:

`Dockerfile`:
~~~Dockerfile
FROM python:3.8.4-buster

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY migrations/ ./migrations/
COPY connect4/ ./connect4/

ENV PYTHONPATH=/app

COPY entrypoint.sh .

CMD [ "/bin/bash", "entrypoint.sh" ]
~~~

`entrypoint.sh`:
~~~bash
#!/bin/bash

set -e

python connect4/app.py db upgrade
python connect4/app.py runserver --host "${HOST}" --port "${PORT}"

exit 0
~~~

Now we will delete the data in the databases we have on the MicroK8s cluster, if this were
more than a school project we would keep the data and migrate it over to our new schema.
But we will skip that to keep things simple.

~~~bash
# capacity
kubectl delete namespace capacity
kubectl delete persistentvolume connect-four-database-capacity

# acceptance
kubectl delete namespace acceptance
kubectl delete persistentvolume connect-four-database-acceptance

# production
## deployment - scale it down to 0 pods so that it does not recreate the old database schema.
kubectl scale deployment connect-four-server-production --replicas=0
## database
kubectl delete service               connect-four-database-production
kubectl delete statefulset           connect-four-database-production
kubectl delete persistentvolumeclaim connect-four-database-production
kubectl delete persistentvolume      connect-four-database-production
kubectl delete storageclass          connect-four-database-production
kubectl delete configmap             connect-four-database-production
~~~

Now create the production database again like you did on day 6, you do not have to
do this for the acceptance and capacity environments, the CI pipeline will recreate
them.

Commit the code changes to github, the pipeline should work.

## Part 3 - Database Migrations

Now that we ORM and migration framework up and running lets try to update our schema:

Add `created` field to your game entity:

~~~python
created = db.Column(db.DateTime, nullable=False, default=datetime.utcnow)
~~~

Generate the migration plan for the new field:

~~~bash
docker run -p 5432:5432 -e POSTGRES_USERNAME=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=connect-four --name database postgres:12
~~~

~~~bash
export DATABASE_USERNAME=postgres
export DATABASE_PASSWORD=postgres
export DATABASE_NAME=connect-four
export DATABASE_HOST=localhost
export DATABASE_PORT=5432
PYTHONPATH=. python connect4/app.py db upgrade
PYTHONPATH=. python connect4/app.py db migrate -m "add creation time"
~~~

Update the game so that the game's creation time is returned in the BaseGameResponse.

### Part 4 - Bonus

Setup `Dependabot` for your client and/or server repository to scan for vulnerabilities in
your npm/pypi packages.

Remember to list this in your infrastructure `README.md` file.

## Handin

You should store all the source files in your repository:

connect-four-server repository:
```bash
.
├── .circleci
│   └── config.yml
├── connect4
│   ├── __init__.py
│   ├── app.py
│   ├── app_logic.py
│   ├── config.py
│   ├── converter.py
│   ├── database.py
│   ├── exceptions.py
│   ├── game_logic.py
│   ├── models.py
│   └── tokens.py
├── connect4
│   ├── create_database.sh
│   └── deploy.py
├── migrations
│   ├── alembic.ini
│   ├── env.py
│   ├── README
│   ├── script.py.mako
│   └── unit
│       ├── xxxxxxxxxxxx_initial_migration.py
│       └── xxxxxxxxxxxx_add_creation_time.py
├── tests
│   ├── __init__.py
│   ├── acceptance
│   |   ├── __init__.py
│   |   ├── config.py
│   |   └── test_*.py
│   ├── capacity
│   |   ├── __init__.py
│   |   ├── config.py
│   |   └── test_*.py
│   └── unit
│       ├── __init__.py
│       └── test_*.py
├── .gitignore
├── entrypoint.sh
├── database.yaml.template
├── Dockerfile
├── docker-compose.yaml
├── k8s.yaml.template
├── README.md
├── requirements.txt
├── requirements_dev.txt
└── requirements_test.txt
```
