# E2E Testing

Tuesday 08.12.2020

## Prerequisites
Before starting this part of the assignment we need to make sure we have the following up and running:
- [ ] A working Connect4 Game client application
- [ ] A running Circle CI pipeline for your client that:
    - On every push:
        - [ ] Builds a docker image from your code
        - [ ] Publishes a docker image from your code
    - On every merge to the main branch:
        - [ ] Builds a docker image from your code
        - [ ] Publishes a docker image from your code
        - [ ] Deploys your application to your kubernetes cluster

## Get up to date
You don't need everything from Week 2 to finish this part of the assignment. Make sure that your client is working, and you're able to play a local game, if it's not you can find the client base here, which will also be used for this assignment:
- [The client code can be found here](https://github.com/hgop/connect-four-client-base)
Make sure to replace the Team name and docker repository field.

## Objectives
Today we want to run E2E tests on our client.

PLEASE NOTE: In a production system we would always want to run E2E when deploying both client and server. If the E2E fail we would like to prevent deployment. E.g. if we make changes to our server we want the E2E tests to run before deploying to production and if they fail it's likely that our changes break the integration between our server and our client. However to make this simpler we're only running the E2E test on our client in this assignment. Also, we're testing the client against our production server and database, we would not want to do this in a live system.
What we want to finish today:

- [ ] Add E2E tests to our client repository
- [ ] Deploy an instance of our client to run the E2E tests on
- [ ] Run our E2E test on our E2E client instance
- [ ] Prevent our client from being deployed if the E2E test fail

## Step 1 - Add E2E tests to your project
We are going to use Cypress to create our E2E tests.

> What is Cypress?

> It is a next-generation front end testing tool constructed for the modern web. This tool addresses the critical pain points developers, and QA engineers used to face when testing modern applications, E.g., synchronization issues, the inconsistency of tests due to elements not visible or available.

> It is built on Node.js and comes packaged as an npm module. As its basis is Node.js, it uses JavaScript for writing tests. But 90% of coding can be done using Cypress inbuilt commands, which are easy to understand.

Setting up Cypress is very simple, but since we're not creating a separate cypress project for our test you can just copy/paste the e2e folder from the connect-four-client-base project, along with minor changes in other files.

Since we need to deploy a seperate instance of our client to run our tests against please copy/update the following files/folders from [this repository](https://github.com/hgop/connect-four-client-base).
```
.
├── .circleci
│   └── config.yml <- Use the deploy script to deploy your application (change the run in the deploy job)
├── e2e <- This entire folder holds our test setup
│   ├── cypress
│   │   ├── fixtures
│   │   ├── integration
│   │   │   ├── *.spec.js
│   │   │   └── ...
│   │   ├── plugins
│   │   │   └── ...
│   │   └── support
│   │       └── ...
│   ├── cypress.json <- This file needs to include your URL
│   ├── package-lock.json
│   └── package.json
├── scripts
│   └── deploy.sh <- We'll use this to deploy our application from now on
├── .dockerignore <- We'll want to exclude the e2e and script folder
├── .gitignore <- We'll want to exclude ALL node_modules folders in the repository and the e2e/videos
└── k8s.yaml.template <- We'll be including an ENVIRONMENT variable (make sure to replace the [TEAM_NAME], [DOKCER_USERNAME] and [DOCKER_REPOSITORY] with your values)
```
When you've finished adding all the code you should be able to run the tests in a browser in your local environment, like so:
```
cd e2e
npm install
npm run open
```
You can also run the tests in a terminal from inside the e2e folder (that's what the CI will do), like so:
```
npm test
```
This will run the tests on the url you added to the `cypress.json` file. If you want to run your client locally and run the tests on your local instance you can change the `baseUrl` in `cypress.json` to `http://localhost:3000`.

## Step 2 - Run E2E tests in your pipeline
To run your tests in Circle CI we'll need to add two jobs in our Circle CI config. The first job deploys an e2e instance of our client so we can run our tests, add this jobs to your config:
```
  e2e-deploy:
    docker:
      - image: circleci/buildpack-deps:stretch
    steps:
      - checkout
      - kubernetes/install-kubectl
      - kubernetes/install-kubeconfig:
          kubeconfig: KUBECONFIG_DATA
      - run: kubectl delete namespace e2e || exit 0
      - run: kubectl create namespace e2e
      - run: bash scripts/deploy.sh "e2e" "e2e" "${CIRCLE_SHA1}"
```
The second job will run the tests on the e2e instance:
```
  e2e-test:
    docker:
      - image: circleci/node:12.9.1-browsers
    steps:
      - checkout
      - run: cd e2e && npm install && npm test
```
Remember to add these jobs to the correct place in your Circle CI config workflow section.
Make sure your pipeline runs without failure.

Make sure your test are calling the e2e environment.

## Step 3 - Add more E2E tests
Our current test runs through one game. It would catch a lot of errors that could come up. E2E tests are usually not used to tests minor details and we won't do that either. Just create two more tests:
1. should display a win for Player 2 where he wins via a diagonal row
2. should display a draw

NOTE: a local game does not display a draw. You'll need to add this functionality and test it.

ALSO NOTE: The current test has 500ms wait time between turns, this is for the server to catch up and prevent false negatives (which are unfortunately a bit common in these types of tests, but can be mitigated). If your test fails make sure you're giving the server and client enough time.

A great introduction on how to use Cypress can be found on their [website](https://docs.cypress.io/guides/core-concepts/introduction-to-cypress.html).

## Handin

You should store all the source files in your repository:

connect-four-client repository:
```bash
.
├── .circleci
│   └── config.yml
├── e2e
│   ├── cypress
│   │   ├── fixtures
│   │   ├── integration
│   │   │   ├── *.spec.js
│   │   │   └── ...
│   │   ├── plugins
│   │   │   └── ...
│   │   └── support
│   │       └── ...
│   ├── cypress.json
│   ├── package-lock.json
│   └── package.json
├── public
│   ├── ...
│   └── index.html
├── scripts
│   └── deploy.sh
├── src
│   └── ...
├── .dockerignore
├── .gitignore
├── Dockerfile
├── k8s.yaml.template
├── tsconfig.json
├── README.md
├── package-lock.json
└── package.json
```
