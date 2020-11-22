# Week 1 Assignment

Friday 27.11.2020

**Turn in your GitHub repository URLs into Canvas solo or in pairs (please add `kthorri`, `fanneyyy` and `ironpeak` as collaborators)**

You should store all the source files in your repository:

infrastructure repository:
```bash
.
├── 
├── assignments
│   ├── day01
│   │   └── answers.md
│   └── day02
│       └── answers.md
├── scripts
│   └── setup_local_dev_environment.sh
├── .gitignore
├── instances.tf
├── provider.tf
├── README.md
├── security_group.tf
├── terraform.tfstate
└── terraform.tfstate.backup
```

connect-four-client repository:
```bash
.
├── .circleci
│   └── config.yml
├── public
│   ├── ...
│   └── index.html
├── src
│   ├── ...
│   ├── index.tsx
│   └── App.tsx
├── .dockerignore
├── .gitignore
├── Dockerfile
├── k8s.yaml.template
├── tsconfig.json
├── package-lock.json
└── package.json
```

They must be placed at these location to get full marks.

Add a `infrastructure-repository/README.md` file, it should include the URL to the instance running
the game client `connect4.{{team-name}}.hgopteam.com`.

If you did anything extra, you should list it up in the `infrastructure-repository/README.md` if you
want us to take it into account.
