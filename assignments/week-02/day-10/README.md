# Week 2 Assignment

Friday 04.12.2020

**Turn in your GitHub repository URLs into Canvas solo or in pairs (please add `kthorri`, `fanneyyy` and `ironpeak` as collaborators)**

Your Circle CI Pipeline should be running:
- Lint
- Unit-Test
- Build
- Publish
- Acceptance-Test <- Server only
- Capacity-Test <- Server only
- Deploy

You should store all the source files in your repository:

connect-four-client repository:
```bash
.
├── .circleci
│   └── config.yml
├── public
│   ├── ...
│   └── index.html
├── src
│   ├── components
│   │   ├── App
│   │   │   ├── App.tsx
│   │   │   ├── App.test.js
│   │   │   └── ...
│   │   ├── Board
│   │   │   ├── Board.tsx
│   │   │   ├── Board.test.js
│   │   │   └── ...
│   │   ├── Column
│   │   │   ├── Column.tsx
│   │   │   ├── Column.test.js
│   │   │   └── ...
│   │   ├── LocalCoopGame
│   │   │   ├── LocalCoopGame.tsx
│   │   │   ├── LocalCoopGame.test.js
│   │   │   └── ...
│   │   ├── OnlineMultiplayerGame
│   │   │   ├── OnlineMultiplayerGame.tsx
│   │   │   ├── OnlineMultiplayerGame.test.js
│   │   │   └── ...
│   │   ├── StartGame
│   │   │   ├── StartGame.tsx
│   │   │   ├── StartGame.test.js
│   │   │   └── ...
│   │   ├── Tile
│   │   │   ├── Tile.tsx
│   │   │   ├── Tile.test.js
│   │   │   └── ...
│   │   └── index.tsx
│   ├── external_services
│   │   └── game_api_client.ts
│   ├── serviceWorker.ts
│   ├── react-app-env.d.ts
│   ├── index.css
│   └── index.tsx
├── .dockerignore
├── .gitignore
├── Dockerfile
├── k8s.yaml.template
├── tsconfig.json
├── README.md
├── package-lock.json
└── package.json
```

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

They must be placed at these location to get full marks.

Your `infrastructure-repository/README.md` file should include the URL to the instances running:
- The game client `connect4.{{team-name}}.hgopteam.com`
- The game server `connect-four-server-production.{{team-name}}.hgopteam.com`

If you did anything extra, you should list it up in the `infrastructure-repository/README.md` if you
want us to take it into account.
