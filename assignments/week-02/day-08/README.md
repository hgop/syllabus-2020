# Acceptance Testing

Wednesday 02.12.2020

## Prerequisites
Before starting this part of the assignment we need to make sure we have the following up and running:
- [ ] A running Circle CI pipeline for client and server
- [ ] A live working game with server

## Objectives
Today we want to add additional steps to make sure we don't deploy our applications without them working correctly
- [ ] Add acceptance testing to your server pipeline
- [ ] Add acceptance testing to your server

## Part 1 - Connect Four Server

### Part 1 - Setup acceptance tests

Add `requests` package to your test requirements.

Create the following directory structure:

~~~
.
└── tests
    ├── __init__.py
    └── acceptance
        └── __init__.py
~~~

`tests/acceptance/config.py`
~~~python
import os

API_URL = os.environ["API_URL"]
~~~

`tests/acceptance/test_status.py`
~~~python
from tests.acceptance import config
from typing import List
import requests

def test_status():
    response = requests.get(config.API_URL + "/status")
    assert "Running" == response.text
~~~

Test your acceptance test against your production url:

~~~bash
API_URL=http://connect-four-server-production.{{team-name}}.hgopteam.com/ pytest tests/acceptance
~~~

### Part 3 - Deploy acceptance environment

We will deploy another instance of our game server to test against in it's own namespace in
kubernetes.

~~~yaml
  acceptance_deploy:
    docker:
      - image: circleci/buildpack-deps:stretch
    steps:
      - checkout
      - kubernetes/install-kubectl
      - kubernetes/install-kubeconfig:
          kubeconfig: KUBECONFIG_DATA
      - run: kubectl delete namespace acceptance || exit 0
      - run: kubectl create namespace acceptance
      - run: bash scripts/create_database.sh "acceptance" "acceptance"
      - run: bash scripts/deploy.sh "acceptance" "connect-four-server" "acceptance" "${CIRCLE_SHA1}"
~~~

You can check the status of pods by using the namespace flag:

~~~bash
kubectl get pods --namespace acceptance
kubectl get pods -n acceptance
~~~

### Part 4 - Run acceptance tests

Run your acceptance tests agains the newly deployed instance:

~~~yaml
  acceptance_test:
    executor: python
    steps:
      - checkout
      - run: pip install -r requirements_test.txt
      - run: pytest --cov=connect4 tests/acceptance
    environment:
      API_URL: "http://connect-four-server-acceptance.[TEAM_NAME].hgopteam.com/"
~~~

### Part 5 - Create another test

Create a test that plays through an entire game, simulating two users.

Here is a helper class you can use:

`tests/acceptance/helper.py`
~~~python
from connect4 import models, converter
from tests.acceptance import config
from typing import List
import requests

class InitializedGame:
    gameId: str
    playerOneId: str
    playerTwoId: str

    def __init__(
        self,
        gameId: str,
        playerOneId: str,
        playerTwoId: str
    ) -> None:
        self.gameId = gameId
        self.playerOneId = playerOneId
        self.playerTwoId = playerTwoId

def initialize_game() -> InitializedGame:
    response = requests.post(config.API_URL + "/create_game")
    assert response.status_code == 201

    json = response.json()
    gameId = json["gameId"]
    playerOneId = json["playerId"]

    response = requests.post(config.API_URL + "/join_game", json={
        "gameId": gameId
    })
    assert response.status_code == 202

    json = response.json()
    playerTwoId = json["playerId"]

    return InitializedGame(
        gameId=gameId,
        playerOneId=playerOneId,
        playerTwoId=playerTwoId
    )

def make_move(gameId: str, playerId: str, column: int) -> models.MakeMoveResponse:
    response = requests.post(config.API_URL + "/make_move", json={
        "gameId": gameId,
        "playerId": playerId,
        "column": column
    })
    assert response.status_code == 202

    json = response.json()
    return models.MakeMoveResponse(
        gameId=json["gameId"],
        active=json["active"],
        playerId=json["playerId"],
        winner=converter.optional_int_to_player(json["winner"]),
        playerNumber=converter.int_to_player(json["playerNumber"]),
        activePlayer=converter.int_to_player(json["activePlayer"]),
        playerCount=json["playerCount"],
        board=converter.str_to_board(converter.board_to_str(json["board"])),
    )
~~~

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
├── scripts
│   ├── create_database.sh
│   └── deploy.py
├── tests
│   ├── __init__.py
│   ├── acceptance
│   |   ├── __init__.py
│   |   ├── config.py
│   |   └── test_*.py
│   └── unit
│       ├── __init__.py
│       └── test_*.py
├── .gitignore
├── database.yaml.template
├── Dockerfile
├── docker-compose.yaml
├── k8s.yaml.template
├── README.md
├── requirements.txt
└── requirements_dev.txt
```
