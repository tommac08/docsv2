page_title: Shippable Formations UserGuide | Shippable
page_description: Learn how to use Shippable Formations
page_keywords: containers, deployment, formations, continuous deployment, kubernetes, Dev/Test labs

# User Guide

Using this you will learn the following:

1. How to set up a simple two-tier app on Shippable Formations
**ADD LINK TO RADAR**

2. Deploying and Accessing your application via an Endpoint
3. Scaling your Application within a formation by adding replicas
4. How Ports work and when you should be exposing these
5. Compose your templates to deploy to Multiple Environments
6. Gotchas/Key Concepts


# Admin User Guide

This is a guide for the owner of the Shippable Formation to set up and manage formations


# Adding Images to your Formation

## Step Zero: Set up Account Integration

## Step 1:
Shippable Formations allows you to manage your multi-container applications across multiple
environments with just a few clicks.
A formation is comprised of images, configuration variables and environments. Once these are set up
in your formation, you will be ready to do one-click deployments and rollbacks across your environments.


## How do you create a Shippable Formation?



**TO BE UPDATED AFTER THE FLOW IS COMPLETE**

Choose Formation as the Subscription Type when you sign up (sign up link).
At this point, you will need to choose the number of containers you want to buy. At a minimum,
number of containers is equal to the number of environments * number of containers per environment.

```Example Application goes here```

# Formation Environment

A formation environment is a containerized replica of your production environment. You can manage
the number of replicas in your setting. They consist of one or more instances of formation templates.
Only one version of a formation template is active in an environment at any point.

# Formation Deploy Template

Formation Deploy Template is the combination of images and configuration that forms the **service**, an instance of which is deployed
to an environment

# Formation Deploy State:

Everytime a template is deployed to an environment, the state is stored and we refer to this as **Deploy State**. The history of Deploy States
that are stored enables you to look at historical deployments or rollback to a previous state.

