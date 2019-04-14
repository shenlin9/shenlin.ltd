---
title: Google Cloud Platform - GCP, G Suite
categories:
  - Cloud
tags:
  - Cloud
  - GCP
  - G Suite
---

---from---> On-Prem(on-premises software) ---to---> Cloud

Cloud is just a set of tools that helps you, the developer, spend less time
managing and more time being created as a developer.

<!--more-->

## GCP & G Suite

GCP(Google Cloud Platform) : infrastructure, platform

G Suite : Google Docs, Google Drive...the software as a service

Cloud: infrastructure, platform, software as a service

    IaaS : Infrastructure as a Service
    PaaS : Platform as a Service
    SaaS : Software as a Service

Good Cloud 是 GCP 和 G Suite 的混合体。

## The History of GCP

2012 年时的 GCP 组成：
* Compute Enginee
* App Enginee
* Cloud Storage
* Big Query

目前的 GCP 组成, More than 60 GCP Services：
* Compute
  * Compute Enginee
  * App Enginee
  * ......
  * ......
* Storage and Database
  * Cloud Storage
  * ......
  * ......
* Networking
  * ......
  * ......
* Big Data
  * Big Query
  * ......
  * ......
* Machine Learning
  * ......
  * ......
* Identity & Security
  * ......
  * ......
* Management Tools
  * ......
  * ......
* Developers Tools
  * ......
  * ......

Paradox of choice: less is more, too much choice is stressful

So Focus On Four Areas:
* Compute
  * Virtual Machines
  * Containers
  * Serverless (deploy your codes and let someone else manage that for you)
* Storage and Databases
* Machine Learning
* Big Data

## Kubernetes

Containers Alone Are Not Enough:

If you have a complex application, with a bunch of front-end servers, a bunch of
back-end database servers, some middleware, it's complicated to get all that
stuff to work together, to monitor it, to restart things when they fail. That's
where orchestrators come in.

So We've got a new breed of software, container orchestration, the most popular
one being Kubernetes.

## Lstio

Another nice patter for you to reuse is the service access layer, so if you want
to apply some security policies, telemetry, capture analytics, customize how
your service is accessed, it's really nice to have a service layer on top of it.
And that's lstio is all about as a servcie mesh.

All the combination of all these innovations together: Containers, Orchestrators
and a service mesh really gives you a powerful way to manage and deploy your
apps, especially in the cloud.

## Products To Deploy

What do you have on Google Cloud as products to deploy applications?

==============================================================================

Compute Enginee     Container Engine        App Enginee     Cloud Functions

-----Highly Customisable---------------------------Hightly Managed------------

Cloud Launcher      Container Builder
                    Container Registry

==============================================================================

Cloud Launcher : a marketplace for different kinds of solutions, for example,
you can install LAMP Stack, WordPress, Asp.net Framework with one click.
??? Cloud Launcher is for Compute Enginee, such as Virtual Machine ???

How do you run containers on Google Cloud?
The easiest way to do that is App Engine: you take your code, you deploy using
Google Cloud, then we create a container for it in the cloud, and then we host
it on Container Repository, and then we deploy it to App Engine.

Sometimes you want to be able to define multiple containers, and you want to
be able to scale them independently, so you basically want to create a container
cluster. For that, we have Container Engine, Container Engine is basically
managed Kubernetes.

Some tools around Containers:
Container Builder : is a way to build containers in the cloud really fast.
Container Registry : Once you've built your containers, they get hosted into
this private space for your project, once you have the container image, you can
deploy it to Compute Engine or App Engine or Container Engine.

Cloud Functions : you create a node.js function, and you define what trigger
that function, it can be get triggered by HTTP calls or by pop-up messages.You
just deploy that and don't worry about container or VMs or anything like that.
Everything is fully managed for you.

## Others

Orchestration Systems


Kubernetes : Kubernetes is the container orchestrator















