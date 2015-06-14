page_title: Learn About What Makes Shippable Great | Documentation | Shippable
page_description: Code examples, FAQs, language & platform support
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

## What is Shippable?

Shippable is a SaaS platform for developers and devops teams that significantly reduces the time taken for code to be built, tested and deployed to production.

Shippable comprises of two products that enables you to ship code faster -

**Shippable CI:** Shippable CI is our Continuous Integration and Deployment Platform. It uses **Build Minions** which are docker based containers to run your workloads. After building and testing your code, you can push your docker image to Docker Hub, Google Container Registry or any other private registry.

Go to [Shippable CI overview](ci_overview.md) to learn more.

**Shippable Formations** Shippable Formations is a system based on Kubernetes that allows you to manage your multi-tier application across multiple environments without writing any DevOps code. It is persistent, fully orchestrated and scalable.

Go to [Shippable Formations Overview](formations_overview.md) to learn more.

*****

## Quick Start: Run a build

![Run a Build](images/build_flow.gif)

### Step 0: Prerequisite

Shippable uses either your [Github](https://github.com) or [Bitbucket](https://bitbucket.org) account to authenticate. You must have your source code in one of these two repos to sign up for Shippable.

### Step 1: Sign in to Shippable

To sign in, visit the [Shippable website](https://www.shippable.com),
click **Login**, and choose between Github or Bitbucket auth. This will take you to either the Github or Bitbucket Sign In page, where you enter your credentials.

### Step 2: Allow Access to repos

Click `Authorize Application` on Github or `Grant access` on Bitbucket to allow access to repos.

And Voila! you are now ready to test and run your build on Shippable.

### Step 3: Create YML file

Create a shippable.yml file at the root of the repository you want to build with Shippable.

Your yml needs a couple of entries at the very minumum - the language and the version(s) of the language you want to test against. We will use defaults for the other settings in this case.

```
# language setting
language: nodejs

# version numbers, testing against two versions of node
node_js:
    - 0.10.25
    - 0.11
```


### Step 4: Enable CI for repos

To enable a repository for CI -

- Click on the 'CI' on the Shippable landing page
- Find your subscription from the dropdown and go to your Subscriptions page
- Click on the (+) to enable a new project
- Find the repo you want to enable and click the `Enable` (the key icon) button
- Your project is now enabled and you can see it under your `Project Status`

### Step 5: Run the build

- On the Project Page, click on the `Run Build` icon to run a manual build

This is a very basic version of a build. Go [here](build_case2.md) to learn how to customize your build further.

A complete reference to your project settings and build configuration is available [here](project_settings.md).

*****

## Quick Start: Create a Formation

*** TO ADD ONCE THE FLOW IS FINALIZED ****



*****

## FAQ

### How can I update my Shippable plan?

Go to `Settings` under either your CI Subscription Page or Formations Page. You can update your plan from either of these places.

Documentation:[Subscription Plan Management](ci_settings/#subscription-plan-management)

### How do I link my github and bitbucket accounts?

Check out our documentation on [linking bitbucket and github accounts](account_settings/#linking-your-bitbucket-and-github-accounts)

### Why can't I see some of my repositories in my Shippable account?

This happens due to one of the following reasons:

- You haven't enabled private repositories in your Shippable account. Go to our [Account Settings Documentation](account_settings.md) to learn how to turn on Private Repos for your Github Account.
- Your account hasn't yet been synced with the latest permissions from GitHub. To force sync your account, click on the `Force Sync` icon in your Account Settings. (the `gear` on the top nav bar)
-  You're a BitBucket user and you have mercurial repositories. We do not support mercurial at this time, so you will need to convert them to git or use another platform for CI.

Go to our [FAQ Page](faq.md) for more.

