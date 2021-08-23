# Yohan Lascombe implementation of Algolia assignment

This repository contains code to build a mini infrastructure (based on a local Kubernetes) to host instant-search application.
It will contains also comments of my work.


# Tradeoff
* the most rapid (or at least a rapid one) way to deploy app on a Virtual Machine is to launch VM with vagrant, then use ansible provisionner to deploy app on this VM with linux system services (systemd) or via docker-compose : it is not what I will do, I want to deploy app on a Kubernetes cluster, but add Kubernetes in the architecte map should be discussed for real application.

# Main steps

As I don't know exactly how much time is needed to achieve this assignment (and also how much time I can spent) : I have cut my work on somes steps. I choose to work first on things I estimate most prio. So here the steps I see:
1. understand what is the subject apps (run locally and look rapidly code)
1. launch app locally with docker (and docker-compose)
1. build docker image with CI
1. deploy app on Kubernetes running on my laptop (with Kind)
1. instrument (with infra as code) how to initialize and run a local Kubernetes cluster
1. configure logs management, monitoring for app in cluster 

In a real context, I will implement CI/CD pipeline to do all these tasks


# Prerequisites

* vagrant (with an hypervisor behind)
* plugin vagrant
    * `vagrant plugin install vagrant-hostmanager`

# Launch locally

Temporarly:
```sh
echo "192.168.121.60  argocd.localhost argocd.mycluster.algolia  instant-search.mycluster.algolia" | sudo tee -a /etc/hosts
```

```bash
vagrant up
```

Wait for vagrant and ansible ends without error and then check in ansible logs login/password (printed just for demo here) and then
authenticate on [ArgoCD](https://argocd.mycluster.algolia/)

## Force ansible relaunch

```sh
vagrant provision
```

# Session 1 : lookup project and run it

## Session Goals
For the first session, my objective are:
* understand what is the app and run it locally
* setup a first CI (without CD for now) pipeline to build docker image and publish it on docker registry (hub.docker.com)

## Choices
* why docker ? for me it is almost a standard and I want to use Kubernetes to deploy the app on the next session
* why docker-compose ? at first seen, I had identified 2 compoents : a front and a ruby part to inject test data. So use docker-compose directly enable to manage them directly.
* I had seen a `.travis.yml` file exists on repository, I am used to play with gitlab (and gitlab-CI) for quite all my projects, so I will take advantage of this exercise to find out TravisCI

## Achievements
* Basics `Dockerfile` and `docker-compose.yml` that enable to launch app locally rapidly
* Simple CI pipeline on TravisCI to build docker image and launch some tests (Trivy for security scan and npm security audit)

## Target CI/CD Pipeline:

### Tests pyramid
Automated tests should be added for each new feature or bugfix
* priorize unit test
* integration test

### Security tools
* [ ] Software Composition Analysis (SCA) : detect component (framework/package) with known issues : `npm audit`
* [x] Container Composition Analysis (CCA) : detect middleware with known issues : `clair` or `trivy`
* [ ] Static Application Security Testing (SAST) : detect bad usage of language or framework (by source code analysis of applications) which could causes applicative issues (XSS, SQLi, …) : `eslint security`
* [ ] Dynamic Application Security Testing (DAST) : execute security tests in order to identify configuration error (HTTPS config, cookie setting...) at infra or applicative level : `ZAP`
* [ ] Runtime application self-protection (RASP) : security framework, included in application, which check app configuration and intercept each query to analyse them: `OpenRASP (Baidu)`









# Session 2: setup local Kubernetes to run the application

## Session Goals
In the assignment description, it is written that it should be appreciated if the work can launched locally. Deploy on the cloud apps is quite the default (and easiest way) to deploy apps. 
So, I will try to create a local cluster with code.

## Choices
* not trivial one : which distribution/packaging of kubernetes to use ? I have considered K3S, Kind and minikube as a short list. I choose Kind for this assignment. I had estimated minikube as a little big, 
and K3s was really interesting but the usage with docker for Kind decide me to use it. If I have to choose again, I do not konw if I do the same choice.
* As it is written deploy on a local VM for example on assignement, I have decided to complexify a little more the setup by hosting the Kubernetes cluster oln a virtual machine (I consider it is not a good choice)
* I am using `vagrant` to manage my hypervisor (VirtualBox) : it is only because I had already used this tool, but for me the good choice will be a `terraform` like tool since it will be probably used to manage real environment. 
I had seen [this repo](https://github.com/bmatcuk/terraform-provider-vagrant) to launch vagrant with terraform but I consider that too complex, for me the best is to replace vagrant by terraform and not launch vagrant with terraform.
* in vagrant, I have configured 2 provisionners : 
    * `docker` : just to install docker
    * `ansible`: install and configure tools required (kubectl, helm, kind, instanciate cluster, deploy ingress controller, deploy argocd)
* 
## Achievements
* vagrant create a VM and launch ansible
* ansible install a mono-node kubernetes cluster and deploy nginx ingress controller and argocd on top
* some bad network configuration to allow host machine to access to VM and some disabling of TLS to can access to argocd without HTTPS (else a valid DNS is required to generate SSL certificate with cert-manager for example)


# Session 3: make the CD part of the pipeline with ArgoCD

## Session Goals
Configure a project and a repository in ArgoCD to deploy instant-search app

## Choices
## Achievements


# Architecture

![](./docs/archi-basic.png)