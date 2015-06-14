page_title: Learn About What Makes Shippable Great | Documentation | Shippable
page_description: Code examples, FAQs, language & platform support
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

## What is Shippable?

Shippable is a SaaS platform for developers and devops teams that significantly reduces the time taken for code to be built, tested and deployed to production.

Shippable comprises of two products that enables you to ship code faster -

### Shippable CI
Shippable CI is our Continuous Integration and Deployment Platform
which is lightweight, super simple to setup, and runs your builds and tests
faster than any other service. It uses **Build Minions** which are docker based
containers to run your workloads.  After building and testing your code, you can push your docker image to Docker Hub, Google Container Registry or any other private registry. You
can also deploy it to any PaaS provider like Heroku & OpenShift and also to
VMs, bare metal, OpenStack clusters, or any major infrastructure
provider.

With Shippable CI, you can:

- automate the packaging and deployment of web applications
- automate testing and continuous integration/deployment

### Shippable Formations
Shippable Formations is a system based on Kubernetes that allows you to manage your multi-tier
application across multiple environments without writing any DevOps code. It is persistent, fully orchestrated and scalable. Each of your developers can now have their own fully integrated test environment at a fraction of the cost!

With Shippable Formations, you can:

- manage multi-container environments that can be easily configured by developers or DevOps teams
- no code deployment and rollback
- easily integrate with existing services
- have persistent, fully orchestrated and scalable dev/test labs
- automate deployment pipelines without machine provisioning

## Sign Up for Shippable

### Step 0: Prerequisite

Shippable uses either your [Github](https://github.com) or [Bitbucket](https://bitbucket.org) account to authenticate. You must have your source code in one of these two repos to sign up for Shippable.

### Step 1: Sign in to Shippable

We use OAuth authentication. What this means is if you have either a Github or Bitbucket account, you do not need to create a separate account on our platform.

To sign in, visit the [Shippable website](https://www.shippable.com),
click **Login**, and choose between Github or Bitbucket auth. This will take you to either the Github or Bitbucket Sign In page, where you enter your credentials.

### Step 2: Allow Access to repos

After entering your credentials in the previous step, you will be prompted to give Shippable
access to your repos. GitHub and Bitbucker auth behave a little differently as follows -

**GitHub**- By default, we will only ask for access to public repos. If
you want to use Shippable to build your private repos, you will need to
authorize us for private repositories. To do that, click on Private
Repos off icon at the top right of your dashboard and go through the
GitHub auth flow. The 'Private Repos' icon should show 'ON' when you
return to the dashboard.

**Bitbucket**- The Bitbucket API does not have public/private
granularity, so we ask for access to all repos on Bitbucket by default.

> **Note**
>
> We realize that most people do not want to give write access to their
> repo. However, we need write permissions to add deploy keys to your
> repos for our webhooks to work. We do not touch anything else in the
> repo.

And you are now ready to test and run your build with shippable! Every user is signed up to our **Free CI Plan** by default. This allows you to run one build concurrently on Shippable CI. Read on further to see how you can run your first build on Shippable or try out a simple formation.

## Quick Start: Run your First Build using Shippable CI

It is super simple to run your first build on Shippable -

![Run a Build](images/build_flow.gif)

### Create YML file

We require a shippable.yml file at the root of the repository you want
to build with Shippable. This yml file is the config for your build.

> **Note**
>
> If you use TravisCI, we support `.travis.yml` natively, so that
> you can test your repos in parallel with Shippable and compare the
> speed and rich visualizations.

Your yml needs a couple of entries at the very minumum - the language
and the version(s) of the language you want to test against.

```python
# language setting
language: node_js

# version numbers, testing against two versions of node
node_js:
    - 0.10.25
    - 0.11
```

The rest of the yml is composed of standard sections - `before_install`,
`install`, `before_script`, `script`, `after_success` or
`after_failure`, and `after_script`.

If you just have the language and language version in your yml, we will
attempt to 'guess' at the other settings and build your project.
However, in most cases, you will need to specify additional
configuration in the yml.

**Complete documentation of YML is available** [HERE](yml_overview/).

### Enable CI for repos

To enable a repository for CI -

- Click on 'CI' on the Shippable landing page
- Find your subscription from the dropdown **update enable project process**


Now, whenever you push a commit to your GitHub/Bitbucket repository, Shippable will build that project as long as you have a shippable.yml (or .travis.yml) at the root of your repository.

### Run the build

Builds can be triggered through webhooks or manually through
shippable.com.

**Webhooks:** Our webhooks are triggered when a commit is pushed to your repo, or if a
pull request is created. Webhooks are a code way to verify that commits to your project build in a clean environment, and not just on the committer's machine.

**Manual Builds:** After enabling the project, click the **Build this project** button to
manually run a build. Instantly, it will redirect you to the build's page and the console log from your build minion starts to stream to your browser through sockets.

## Quick Start: Create a Simple Formation using Shippable Formation

