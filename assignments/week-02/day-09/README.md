# Capacity Testing

Thursday 03.12.2020

## Prerequisites
Before starting this part of the assignment we need to make sure we have the following up and running:
- [ ] A running Circle CI pipeline for client and server
- [ ] A live working game with server
- [ ] An acceptance test that plays one whole game

## Objectives
Today we want to add additional steps to make sure we don't deploy our applications without them working correctly
- [ ] Add capacity testing to your server pipeline
- [ ] Add capacity testing to your server

## Part 1 - Connect Four Server

### Part 1 - Setup capacity tests

Add `pytest-timeout` package to your test requirements.

`tests/capacity/config.py`
~~~python
import os

API_URL = os.environ["API_URL"]
~~~

Where test_game is an acceptance test you created in day 8, that simulates two players playing a game. (if you named it something else you can just change it)

`tests/capacity/test_sequential.py`
~~~python
from tests.acceptance.test_game import test_game
from tests.capacity import config
from typing import List
import requests
import pytest

@pytest.mark.timeout(30)
def test_sequential():
    for _ in range(10):
        test_game()
~~~

Test your capacity test against your production url:

~~~bash
API_URL=http://connect-four-server-production.{{team-name}}.hgopteam.com/ pytest tests/capacity
~~~

### Part 3 - Deploy capacity environment

Setup capacity_deploy like you did for the acceptance tests in day 8.

### Part 4 - Run capacity tests

Setup capacity_test like you did for the acceptance tests in day 8.

### Part 5 - Create another test

Create a test that spawns N number of threads that run X amount of games:

`tests/capacity/test_parallel.py`
~~~python
from tests.capacity import config
from typing import List
import requests
import pytest
import threading
import time

N = 8
X = 10

class State:
    lock: threading.Lock
    gamesPlayed: int
    failed: bool

    def __init__(self) -> None:
        self.lock = threading.Lock()
        self.gamesPlayed = 0
        self.failed = False

    def increment_games_played(self) -> None:
        with self.lock:
            self.gamesPlayed += 1


def play_games(i: int, x: int, state: State) -> None:
    try:
        for j in range(x):
            # TODO test game
            state.increment_games_played()
            print(f"Thread {i}: finished game number {j}.")
    except:
        state.failed = True

# Test: API_URL=http://connect-four-server-capacity.team-name.hgopteam.com/ pytest -s tests/capacity/
@pytest.mark.timeout(120)
def test_parallel():
    threads = []
    state = State()

    for i in range(N):
        thread = threading.Thread(target=play_games, args=(i, X, state))
        threads.append(thread)

    start_time = time.time()

    for thread in threads:
        thread.start()

    while True:
        all_threads_finished = True
        for thread in threads:
            all_threads_finished = thread.is_alive() == False and all_threads_finished
            
        if all_threads_finished or state.failed:
            break

    print(f"Played: {state.gamesPlayed}")
    print(f"Time: {(time.time() - start_time)}")

    for thread in threads:
        thread.join()

    assert state.failed == False
~~~

If there is an assertion error for any game the test should fail immetiately.

Run the test locally against production for:

| N | X |
|---|---|
|  1| 10|
|  2| 10|
|  4| 10|
|  8| 10|
| 16| 10|

Measure for each case (`infrastructure/assignments/day09/answers.md`):
- Time
- Did it complete successfully
- Games played

Now scale up your `connect-four-server` deployment to handle more loadÞ

~~~bash
kubectl edit deployment connect-four-server-production
~~~

And change `spec.replicas` to 3.

You should see 2 more pods start up:

~~~bash
kubectl get pods
~~~

Also change `spec.replicas` in `k8s.yaml.template` to 3 so that it won't be overwritten
during the next deployment. 

(**Bonus**: have the value configurable for each environment: production: 2, acceptance: 1, capacity: 3)

Run the previous test cases again and take those measurements as well.

Do you see any improvements?

Pick some sensible values for N, X and have it run as part of your capacity tests, it should:

- timeout at 30 seconds
- N > 1
- X >= 3
- Take about 15-20 seconds on average in the CI 

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
│   ├── capacity
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
