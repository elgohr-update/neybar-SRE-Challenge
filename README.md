![Build and Deploy](https://github.com/neybar/SRE-Challenge/workflows/Build%20and%20Deploy/badge.svg?branch=main)

# Overview

This project is a sample of how one could wire up a CI/CD pipeline using:

* GitHub Actions
* GitHub Secrets
* Google Cloud
* Terraform
* Docker

Before I started this particular project I hadn't used Github Actions of GCP.  I'm quite used to AWS and a variety of other CI/CD platforms, so my hope was that the new stack would feel comfortable and relatable to previous bodies of work.

After concluding this project, I can say that moving between AWS and GCP is done with relative ease.  The lingo is familiar, and the concepts are consistent.  The biggest challenge is just learning the various product names.

GitHub Actions worked surprisingly well.  I plan on using this again for both personal and business projects.

# Challenge

Welcome, you are the newest member of the DynamicEnablement team! You have been hand picked because of your ability to implement DevOps principles in a meaningful way and maximize value.

The DynamicEnablement team has created an app that will provide the answer to everything, but they are not sure how to deploy, scale, or even monitor.  As the SRE, they rely on you to guide them down this path and trust that you will make sure their product is "reliable".

The development team has provided their container for you, and now it is up to you to configure the rest.

As you design and deploy your solution, please make sure you keep these concepts in mind:

* Is everything automated? (IaC, CI/CD)
* Do I have security in place?
* How will this scale? (Cluster)
* How am I notified when a problem occurs?
* What is my reliability? (Monitoring and dashboard)
* How can I improve my reliability, or do I need to?

Ideally, the solution would be in GCP, however AWS would also be acceptable.

A complete solution should include:

* Infrastructure deployed using IaC
* Service deployed
* Automated deployment pipeline
* Monitoring
* SLI/SLO dashboard

# Project Outcome

At the end of this project the following was accomplished:

* Service account with custom role was created.  The role was given exactly the permissions it needed to perform the following tasks.  (see `GHE-Actions-Role.txt`)

* All secrets and config data for both Actions and Terraform plan/apply are stored in GitHub Secrets

* All Pull-Requests will:
	* Verify that the docker container will build
	* Verify that terraform will generate a successful plan
* All Merges to `main` will:
	* Produce a docker container (stored in Google Container Registry).  The docker image gets tagged with `latest`, as well as a more permanent date/sha tag for idempotent deployments.
	* Run terraform contained in the `./terraform` folder
	* Merges to `main` require a passing build from the PR
	* Merges to `main` also require a code review

The result of the terraform apply will:

* Create `Cloud Run` instance called `app1`
	* This instance will scale to a max of 2 instances based on number of concurrent connections
* Allows public access to the container
* Creates a basic alert (I care about costs, so it will alert if I start spending money on the container)
* Creates a `Notification Channel` email which will send emails based on the previous alert

* `Cloud Run` provides some basic monitoring and dashboarding for application health

# TODO

Todo probably isn't the right word, since I'm done with this.  I learned a lot, if I was going to spend more time polishing here are a few things I would do:

* Create a custom dashboard in `Monitoring`.  Depending on the project this could be done within GCP, or any number of other services such as New Relic
* Create additional GitHub actions for deploying to different environments such as qa or staging, etc.
* Create additional actions for depolying a previous sha based on failed tests
* Create a canary type deployment with automated fail over