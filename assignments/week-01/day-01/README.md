# Bash

Monday 23.11.2020

# The Labs
The lab exercises will be introduced each day. To be able to assist as many students as possible we encourage you to submit issues on the [courses Github repository](https://github.com/hgop/syllabus-2020) when you encounter problems.
If you can we encourage you to answer issues from other students.
Before you submit an issue please make sure you've checked the following:
1. Make sure the same issue has not been submitted before, check both open and closed issues. If the same or similar issue has been submitted before you can comment on the issue and/or re-open it.
2. Make sure to describe your issue using the following template, so it's easier to debug the situation:

```
Note: The following template is for guidance only, if you're just asking a question regarding the course structure or setup or you don't feel the template is applicable it's fine. Otherwise, please fill in the following information.

# Expected Behaviour
What I am trying to accomplish

# Current Behaviour
What is currently happening

## Context

Please provide any relevant information about your setup. This is important in case the issue is not reproducible except for under certain conditions.

* Operating System:
* Other tool version you feel are relevant...

# Failure Information (for bugs)

Please help provide information about the failure if this is a bug. If it is not a bug, please remove the rest of this template.

## Steps to Reproduce

Please provide steps for reproducing the issue if you can:

1. step 1
2. step 2
3. you get it...

## Failure Logs

Please include any relevant log snippets, screen shots or files here.
```

# AWS
We'll be using Amazon web services during this course, if you haven't already registered for a student account please to it now by following [these instructions](https://github.com/hgop/syllabus-2020/blob/master/AWS/README.md).

# Getting to know Bash

Introduction into the bash scripting language and learning how to setup our local dev environment.

## Questions (1-2 lines each)

- [ ] What is Linux?
- [ ] What is bash?
- [ ] What is a package manager?
- [ ] What is git?
- [ ] What is npm?
- [ ] What is Pip?
- [ ] What is virtualenv?

## Objectives

- [ ] Create a folder on your machine for the course, called `hgop-2020`, then create a private git repository on Github and name it `infrastructure` and clone it to your hgop-2020 folder.
- [ ] We recommend using Linux, to set up Linux Ubuntu 20.04 on your machine (options include, mono/dual
      boot, Virtual Machine options (VMWare, VirtualBox, Parallels), Boot from
      USB) **(Not necessary on macOS)**
- [ ] Install an editor of choice (e.g. VS Code, Atom, Sublime, etc.)
- [ ] Install a package manager, for macOS install [Homebrew](https://brew.sh/). (apt-get is included in Ubuntu)

## Assignment

Create a bash script `scripts/setup_local_dev_environment.sh` that checks required programs/dependencies.

- [ ] Make sure all bash commands are commented.
- [ ] The script should prompt the user with:
  - [ ] A welcome message that includes the current user’s username (the
        username should not be hard coded).
  - [ ] Information on what the script does
  - [ ] What type of operating system it is running on
- [ ] Display the date and time when the script starts and ends.
- [ ] The script should install the following tools:
  - [ ] brew (if macOS)
  - [ ] git
  - [ ] NodeJS
  - [ ] Python (if not included in your OS)
  - [ ] Pip
  - [ ] virtualenv
- [ ] The script should print versions of all tools.
  - [ ] brew (if macOS)
  - [ ] git 
  - [ ] NodeJS (node)
  - [ ] npm (included with node)
  - [ ] Python
  - [ ] Pip
  - [ ] virtualenv
- [ ] The script should exit with error code on failures.

## Adding an SSH key to GitHub

The following information can be found
[here for creating new key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
and
[here for adding to github](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

Now review the handin information, commit your changes and clone the repository with SSH instead of HTTPS. SSH
is more convenient when automating build systems because it allows for
password-less authentication. It is also more secure e.g. keyloggers.

## Create an issue on Github
Submit a new issue on the course's Github repository with the following information:
- Team members (name, email)
- Your github repository link

## How do I know I'm done?

- [ ] I have answered the questions
- [ ] I have created an executable script that completes all requirements
- [ ] I have commented the script (what is the purpose of each line or function)
- [ ] I've submitted a Github issue

## Handin

This is how your repositories should look after todays assignment which you
will submit on Friday.

infrastructure repository:
```text
.
├── scripts
│   └── setup_local_dev_environment.sh
└── assignments
    └── day01
        └── answers.md
```

They must be placed at this location to get full marks.\
YourGitRepository/assignments/day01/answers.md\
YourGitRepository/scripts/setup_local_dev_environment.sh

You should maintain your setup script through out the 
course for the final handin.

## Tips and tricks
Bash supports functions. Functions in bash behave like commands, not like functions in regular programming
languages. That means they have stdout, stderr and return codes.

Google can help you find a solution to almost every problem in bash. You just have to know how to phrase your search.
