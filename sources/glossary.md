page_title: Glossary | Documentation | Shippable
page_description: Definitions of terms used in Shippable documentation
page_keywords: concepts, documentation, shippable, CI/CD

# Glossary

Definitions of terms used in Shippable documentation

## Subscriptions

A default subscription is created for every user account so that
Shippable can group CI projects under a common entity. A default free
subscription is created on signup with quotas as per our pricing plans.
Users can upgrade this free subscription to a paid plan and increase the number of containers on their subscription.

## Minions

Minions are Docker based containers that run your builds and tests. You
can think of them as Build VMs. Each minion runs one build at a time, so
the more minions you have, the more concurrency you will get.

Minions are automatically provisioned whenever a build is triggered and
it will get deleted after the build finishes execution. We will
automatically add additional minions, as appropriate, based on your
subscription plan.

Each minion starts from a base image and can be customized by specifying
`before_install` and `install` scripts in the YML file. A minion can be configured to
run any package, library, or service that your application requires.
There are some preinstalled tools and services that you can use to
customize your minions even further.

## Projects

A Project is used to handle builds/CI/CD on your repo. You need to
create a project for each repo you want CI enabled. Every project is
under a subscription and your limits/quotas are tracked at the
subscription level.

