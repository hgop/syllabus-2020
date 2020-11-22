# Terraform and Kubernetes

Wednesday 25.11.2020

# Provision environment in AWS.

## Objectives

![System Diagram](diagram_day_3.jpg)

Today we will be setting up MicroK8s in AWS. We will use `Terraform` to create the infrastructure through code. Then we will setup a DNS record to route requests for `*.{{team-name}}.hgopteam.com` to your MicroK8s instance. Then we will deploy the client you created in day 2 to your cluster.

## Step 1 - Setting up a student account
If you haven't already registered for a student account please to it now by following [these instructions](https://github.com/hgop/syllabus-2020/blob/master/AWS/README.md).
Then create a AWS Profile

Setup your AWS Client by logging in to AWS Educate and clicking the `Account Details` and
following the instructions.

<span style="color:red">**IMPORTANT**</span> For some reason an AWS educate session is only 3 hours, so you might need to do this part again if it expires before you finish day 4. None of your automated scripts should take this limitation into account, they can assume that the credentials are valid.

## Step 2 - Getting started with Amazon Web Services

> What is AWS?   
> Amazon Web Services (AWS) is the world’s most comprehensive and broadly adopted cloud platform, offering over 165 fully featured services from data centers globally. Millions of customers —including the fastest-growing startups, largest enterprises, and leading government agencies—trust AWS to power their infrastructure, become more agile, and lower costs. From [amazon.com](https://aws.amazon.com/what-is-aws/)

### Create a Key Pair

> What is a Key Pair?   
> Amazon EC2 uses public–key cryptography to encrypt and decrypt login information. Public–key cryptography uses a public key to encrypt a piece of data, and then the recipient uses the private key to decrypt the data. The public and private keys are known as a key pair. Public-key cryptography enables you to securely access your instances using a private key instead of a password.   
> When you launch an instance, you specify the key pair. You can specify an existing key pair or a new key pair that you create at launch. At boot time, the public key content is placed on the instance in an entry within ~/.ssh/authorized_keys. To log in to your instance, you must specify the private key when you connect to the instance. From [https://docs.aws.amazon.com](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)

Set up your AWS account.
From the Vocareum page click on the AWS console.

Now inside the Console go to:\
`Services > EC2 > Network & Security > Key Pairs`\
Create a new Key Pair named `MicroK8s`, we will use this key later to access our instance.\
So make sure to download it and store it at a safe location you can share it with your other group member so that he also has access to the instance, but **DO NOT** place it inside your repository.\
I recommend placing it in your home directory `~/.aws/keys/MicroK8s.pem`.\
If you lose it, you won't be able to access instances created with it.

## Step 3 - Install AWS CLI

[Install AWS Cli](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html), once you are
done verify using:
```bash
aws --version
```

Configure AWS Client by logging in to Vocareum (AWS Educate) and clicking the `Account Details` and
following the instructions.

The AWS client is a command line tool that is used to create and manage infrastructure in AWS. In
this course we only need it to configure the credentials for AWS so that we can use another tool called
terraform to manage our aws infrastructure (More on that in step 5).

Also remember to add `aws` to the setup script you created in day 1.

## Step 4 - Install Kubectl

[Install Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/), once you are
done verify using:
```bash
kubectl version --client
```

Kubectl is a command line tool to run commands agains a kubernets cluster.

Kubernetes is a open source system for managing containered applications, we will be using MicroK8s
in this course which is a minimal kubernetes installation that runs on a single node (computer).

Also remember to add `kubectl` to the setup script you created in day 1.

## Step 5 - Install Terraform

[Install Terraform](https://www.terraform.io/downloads.html), once you are
done verify using:
```bash
terraform --version
```

Terraform is an automation tool for creating and managing infrastructure in the cloud, you describe
the infrastructure you want in HCL and terraform will do the calculation needed to create or migrate
your existing infrastructure match your description.

We will be using it describe the EC2 instance, we want to run MicroK8s on.

Also remember to add `terraform` to the setup script you created in day 1.

## Step 6 - Infrastructure Repository

Use the infrastructure repository you created on day 1 for the following steps.

## Step 7 - Terraform

In your infrastructure repository create the following files:

`provider.tf`
~~~terraform
# TODO Comment 1-2 sentences.
provider "aws" {
  region = "us-east-1"
}
~~~

`instances.tf`
~~~terraform
# TODO Comment 2-3 sentences.
resource "aws_instance" "microk8s" {
  ami           = "ami-0885b1f6bd170450c" # Ubuntu 20.04
  instance_type = "t3.medium"
  key_name      = "MicroK8s"

  associate_public_ip_address = true

  vpc_security_group_ids = [
    aws_security_group.microk8s_security_group.id
  ]

  tags = {
    Name = "MicroK8s"
  }
}

# TODO Comment 1-2 sentences.
output "microk8s_dns" {
  value = aws_instance.microk8s.public_dns
}
~~~

`security_group.tf`
~~~terraform
# TODO Comment 2-3 sentences.
resource "aws_security_group" "microk8s_security_group" {
  name = "microk8s"

  // ssh
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  // http
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  // k8s
  ingress {
    from_port   = 16443
    to_port     = 16443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  // egress
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
~~~
These files describes the infrastructure you want Terraform to create, in this case we are
telling it that we want to use AWS as our provider (one of many cloud providers supported
by Terraform) and where it can find the credentials run commands on AWS.\
Then we tell it to create an `aws_instance` named `microk8s`, the instance is created using
the provided ami (Amazon Machine Image) in this case a machine running Ubuntu 20.04.

`.gitignore`
~~~.gitignore
# Local .terraform directories
**/.terraform/*

# Crash log files
crash.log

# Exclude all .tfvars files, which are likely to contain sentitive data, such as
# password, private keys, and other secrets. These should not be part of version 
# control as they are data points which are potentially sensitive and subject 
# to change depending on the environment.
#
*.tfvars

# Ignore override files as they are usually used to override resources locally and so
# are not checked in
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# Include override files you do wish to add to version control using negated pattern
#
# !example_override.tf

# Include tfplan files to ignore the plan output of command: terraform plan -out=tfplan
# example: *tfplan*

# Ignore CLI configuration files
.terraformrc
terraform.rc
~~~

Initialize Terraform by:

~~~bash
terraform init
~~~

Now see the terraform execution plan by:

~~~bash
terraform plan
~~~

It's a good habit to read through the execution plan before applying it, since a misconfiguration
could result in a unwanted deletion or a restart of a service. But we are learning here so there
is no risk of that happening.

Apply:

~~~bash
terraform apply
~~~
Terraform will generate a *.tfstate file, you should also add it to .gitignore since it can
contain secrets that do not belong in a public repository.\
The recommended way to keep track of the tfstate file is to store the state remotely that
won't be necessary for this course.\
Keep in mind that this file is used by Terraform to keep track of the resources it is 
managing for you so if you accidentally delete it, it will lose track of your existing
resources. If that happens go to the AWS management console and delete the instances
manually then recreate them using Terraform.

It should complete successfully and output the public DNS for the instance as `microk8s_dns`.

[Setup DNS](https://github.com/hgop/syllabus-2020/issues/1), we will create a DNS record for your
AWS instance so that you have a nicer url to work with. e.g. `team-name.hgopteam.com`.

You should commit everything (including the genrated *.tfstate and *.tfstate.backup files)
these tfstate files should always be included when you make changes to the infrastructure using 
terraform since they contain the identifiers of all the resources terraform is managing.

## Step 8 - MicroK8s

Now ssh into your instance using the keypair you created in step 2.

First you might have to change the permissions on your keyfile:

~~~bash
chmod 400 ~/.aws/keys/MicroK8s.pem
~~~

SSH into your EC2 instance:

~~~bash
ssh -i "~/.aws/keys/MicroK8s.pem" ubuntu@your-ec2-instance.eu-west-1.compute.amazonaws.com
~~~

Now to setup MicroK8s in your AWS instance:

~~~bash
# Update
sudo apt update
sudo apt upgrade

# Install MicroK8s
sudo snap install microk8s --classic --channel=1.19

sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
~~~

Exit the ssh session and then ssh back in for the permission changes to take effect.

Wait for MicroK8s to be ready and then enable the ingress addon.

~~~bash
microk8s status --wait-ready

microk8s.enable ingress
~~~

Add DNS.6 to `/var/snap/microk8s/current/certs/csr.conf.template`:

~~~text
[ alt_names ]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster
DNS.5 = kubernetes.default.svc.cluster.local
DNS.6 = {{team-name}}.hgopteam.com
IP.1 = ...
IP.2 = ...
#MOREIPS
~~~

Get the kubeconfig file using:

~~~bash
microk8s config
~~~

Save the output on your local machine in `~/.kube/config` and change the
`clusters.microk8s-cluster.server` to `https://{{team-name}}.hgopteam.com:16443`, you
should share this file with other group members so they can access the cluster.

(**DO NOT** keep the kubeconfig file in your repository)

The kubeconfig file is used by kubectl to autenticate with your kubernetes instance.

Check that you can connect to your kubernetes cluster from your local machine by doing:

It won't work until the CNAME record has been setup for you [here](https://github.com/hgop/syllabus-2020/issues/1).

~~~bash
kubectl get namespaces
~~~

You should see:

~~~
NAME              STATUS   AGE
kube-system       Active    5m
kube-public       Active    5m
kube-node-lease   Active    5m
default           Active    5m
ingress           Active    5m
~~~

## Step 9 - HttpBin

Now we will deploy our first service to the cluster, we will use a public docker image called httpbin.\
It's a simple service, just to see that everything has been configured correctly for your cluster.

Create the following file:

`httpbin.yaml`
~~~yaml
# TODO Comment 2-3 sentences.
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
spec:
  rules:
  - host: "httpbin.{{team-name}}.hgopteam.com"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: httpbin
            port:
              number: 8000
---
# TODO Comment 2-3 sentences.
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  ports:
  - name: http
    port: 8000
    targetPort: 80
  selector:
    app: httpbin
---
# TODO Comment 3-5 sentences.
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v1
  template:
    metadata:
      labels:
        app: httpbin
        version: v1
    spec:
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: Always
        name: httpbin
        ports:
        - containerPort: 80
~~~

The yaml file contains a description of a service that we can deploy to our kubernetes cluster.\
The deployment tells kubernetes what image it should run, in this case a open source httpbin server listening on port 80.\
The service is an abstraction for a logical set of pods, in this case our single httpbin pod.\
The ingress tells kubernetes to forward requests for url `httpbin.{{team-name}}.hgopteam.com` to the httpbin service port 8000, which maps to port 80 in the container which is the port httpbin listens on.

Now deploy the httpbin service to your cluster:

~~~bash
kubectl apply -f httpbin.yaml
~~~

Open `httpbin.{{team-name}}.hgopteam.com` in your browser.

You should see:

![HttpBin](httpbin.png)

You can see the resources you created in the cluster using kubectl:

~~~bash
kubectl get ingress
kubectl get service
kubectl get deployment
kubectl get pods
~~~

Take the name of the httpbin pod in my case `httpbin-86956d44c4-qt27d` and lets check the logs:

~~~bash
kubectl logs httpbin-86956d44c4-qt27d
~~~

You should see everything that is printed to stdout:

~~~
[2020-11-20 19:39:04 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2020-11-20 19:39:04 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2020-11-20 19:39:04 +0000] [1] [INFO] Using worker: gevent
[2020-11-20 19:39:04 +0000] [8] [INFO] Booting worker with pid: 8
~~~

Once the webpage is up at `httpbin.{{team-name}}.hgopteam.com` you are done.

If you need to restart a pods you can simple delete it:

~~~bash
kubectl delete pod httpbin-86956d44c4-qt27d
~~~

Kubernetes will recreate the pods because there is a httpbin deployment in the cluster, if
you want to remove httpbin completely you can do:

~~~
kubectl delete deployment httpbin
kubectl delete service httpbin
kubectl delete ingress httpbin
~~~

You can also leave the httpbin service running.

## Step 10 - Connect4

Copy the `httpbin.yaml` into the `connect-four-client` repository and
rename it to `k8s.yaml.template`.

Change the `k8s.yaml.template` so that the client is available at: `connect4.{{team-name}}.hgopteam.com`
and you use the image your pushed to docker hub in day 02.

### How do I know I'm done?

- [ ] `httpbin.{{team-name}}.hgopteam.com` is working.
- [ ] `connect4.{{team-name}}.hgopteam.com` is working.
- [ ] My files are in the infrastructure repository.
- [ ] Comment the k8s.yaml.template and terraform files.

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
