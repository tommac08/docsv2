page_title: Learn About What Makes Shippable Great | Documentation | Shippable
page_description: Code examples, FAQs, language & platform support
page_keywords: containers, lxc, Docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

## What is Shippable?

Shippable is a SaaS platform for developers and DevOps teams that significantly reduces the time taken for code to be built, tested and deployed to production.

Shippable is comprised of two products that enable you to ship code faster:

**Shippable CI/CD:** Shippable CI/CD is our Continuous Integration and Deployment Platform. It uses **Build Minions**, which are Docker based containers to run your workloads. After building and testing your code, you can push your Docker image to Docker Hub, Google Container Registry, or any other private registry.

Go to [Shippable CI/CD overview](ci_overview.md) to learn more.

**Shippable Formations** Shippable Formations gives developers an easy way to provision, configure, and deploy multi-container Dev and Test clouds (environments) with zero DevOps code. It is fully integrated with the updated Shippable CI/CD platform, and together, they create an automated, frictionless pipeline from source control to full-topology environments that can mimic your production environment or represent any combination of software component versions you'd like to deploy.

Go to [Shippable Formations Overview](formations_overview.md) to learn more.

*****

## Quick Start: Run a build

![Run a Build](images/build_flow.gif)

### Step 0: Prerequisite

Shippable uses either your [GitHub](https://github.com) or [Bitbucket](https://bitbucket.org) account to authenticate. You must have your source code in one of these two repos to sign up for Shippable.

### Step 1: Sign in to Shippable

To sign in, visit the [Shippable website](https://www.shippable.com),
click **Login**, and choose between GitHub or Bitbucket auth. This will take you to either the GitHub or Bitbucket Sign In page, where you enter your credentials.

### Step 2: Allow Access to repos

Click `Authorize Application` on GitHub or `Grant access` on Bitbucket to allow access to repos.

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


### Step 4: Enable CI/CD for repos

To enable a repository for CI/CD:

- On the Shippable landing page, click on **CI**

![CI](images/landing_widget.gif)

- Find your subscription from the dropdown and go to your Subscriptions page
- Click on the ![add](images/add_icon.gif) to enable a new project
- Find the repo you want to enable and click on the ![enable](images/enable_icon.gif) icon
- This will bring you back to the project dashboard and you can see your enabled project there.

### Step 5: Run the build

- On the Project Page, click on the `Run Build` icon to run a manual build
- You can view the build status on the Console in the build page
- The status is updated as soon as the build completes

>This is a very basic version of a build. Learn how to [customize your build further](build_case2.md)

*****

## Quick Start: Create a Formation

*** TO ADD ONCE THE FLOW IS FINALIZED ****


*****


## FAQ

### How can I update my Shippable plan?

Shippable CI/CD and Shippable Formations are 2 different subscriptions under your account. These need to be purchased separately.

To purchase additional containers for Shippable CI:

- Click on **CI** on the Shippable Landing page
- Click on your **CI Subscription** in the dropdown
- Go to the **Billing** tab
- Make sure your plan is set to `Multi-Tenant CI`
- Use the slider to select the number of containers you want for your subscription.
- Enter your payment details and click `Buy`
- Your CI Plan is now updated

To purchase containers for a new or existing Shippable Formation:

- Click on **Formations** on the Shippable Landing page
- Click on your **Add a new formation** in the dropdown
- This will take you to our payment page. Choose `Multi-Tenant Formation` as your plan
- Use the slider to select the number of containers you want for your formation
- Enter your payment details and click `Buy`
- You are ready to start on your new formation

To update containers in an existing Shippable Formation:

- Click on **Formations** on the Shippable Landing page
- Choose the Formation that you want to update
- Go to the **Billing** tab
- In the first section, choose `Multi-Tenant Formation` as your plan
- Use the slider to select the number of containers you want for your formation
- Enter your payment details and click `Buy`
- Your plan is now updated


### Why can't I see some of my repositories in my Shippable account?

This happens due to one of the following reasons:

- You haven't enabled private repositories in your Shippable account. Go to [Account Settings](account_settings.md) and in the **GitHub Identity** section, click on the **Private Repos OFF** icon. This is a one-way toggle button to turn on Private Repos for your GitHub account.
- Your account hasn't yet been synced with the latest permissions from GitHub. To force sync your account, click on the `Force Sync` icon in your Account Settings (the `gear` on the top nav bar).
-  You're a BitBucket user and you have mercurial repositories. We do not support mercurial at this time, so you will need to convert them to git or use another platform for CI.

Visit our [FAQ Page](faq.md) for answers to more frequently asked questions.

