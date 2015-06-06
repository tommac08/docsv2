page_title: Learn About What Makes Shippable Great
page_description: Code examples, FAQs, language & platform support
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

## Shippable Formations
Shippable Formations allows you to manage your multi-container applications across multiple 
environments with just a few clicks. Behind the scenes, we use Kubernetes clusters that allows us 
to abstract anything related to managing containers or infrastructure for our users.

A formation is comprised of images, configuration variables and environments. Once these are set up 
in your formation, you will be ready to do one-click deployments and rollbacks across your environments.


## How do you create a Shippable Formation?

**TO BE UPDATED AFTER THE FLOW IS COMPLETE**

Choose Formation as the Subscription Type when you sign up (sign up link). 
At this point, you will need to choose the number of containers you want to buy. At a minimum, 
number of containers is equal to the number of environments * number of containers per environment.

```Example Application goes here```

## What happens when a Shippable Formation is created?

## Formation Environment

A formation environment is a containerized replica of your production environment. You can manage 
the number of replicas in your setting. They consist of one or more instances of formation templates. 
Only one version of a formation template is active in an environment at any point. 

## Formation Deploy Template 

Formation Deploy Template is the combination of images and configuration that forms the **service**, an instance of which is deployed
to an environment 

## Formation Deploy State

Everytime a template is deployed to an environment, the state is stored and we refer to this as **Deploy State**. The history of Deploy States 
that are stored enables you to look at historical deployments or rollback to a previous state.

