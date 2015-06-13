page_title: Sign Up to Shippable | Documentation | Shippable
page_description: Getting Started - Sign up
page_keywords: getting started, questions, documentation, sign up, shippable

# Sign up for Shippable

## Step 0: Prerequisite

Shippable uses either your [Github](https://github.com) or [Bitbucket](https://bitbucket.org) account to authenticate. You must have your source code in one of these two repos to sign up for Shippable.

## Step 1: Sign in to Shippable

We use OAuth authentication. What this means is if you have either a Github or Bitbucket account, you do not need to create a separate account on our platform.

To sign in, visit the [Shippable website](https://www.shippable.com),
click **Login**, and choose between Github or Bitbucket auth. This will take you to either the Github or Bitbucket Sign In page, where you enter your credentials.

## Step 2: Allow Access to repos

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

## Step 3: Choose what you want to do

Every user is signed up to our **Free CI Plan** by default. This allows you to run one build concurrently on Shippable CI.

When you get to the landing page, on the drop down, you will see three sections on the dropdown -

**Section 1: Shippable CI**

This section displays all the subscriptions that you are either an owner or a collaborator for. Choose the appropriate subscription from the section to continue onto Shippable CI.

For more details on Shippable CI, go to [Shippable CI Overview](ci_overview.md).

**Section 2: Shippable Formation**

Click on `Add Formation` to add a new formation. Existing Formation Subscriptions will be listed in this section as well.

For more details on Formations, go to [Formations Overview](formations_overview.md).

**Section 3: Lighthouse**

This will lead you to the Lighthouse dashboard that manages images that you want to watch.

To learn more about Lighthouse, go to [Using Lighthouse](lighthouse.md).

## Permissions

We closely mimic GitHub and Bitbucket permissions for Orgs and projects.
Anyone who has access to an organization or repository in
GitHub/Bitbucket will also have access to build information and/or
repository and build actions on Shippable. This happens automatically,
so if you enable a repository in your Org on Shippable and another team
member signs in, they will see the enabled repository and build history
already present in their account.

We support 2 roles -

**Owner :** Owners have all privileges for an Org or Project. They can
enable, run and delete projects, upgrade pricing plans, and view/run,
cancel, and delete builds.

**Collaborator :** Collaborators can enable projects and view/run builds
on Shippable. They cannot delete enabled projects or upgrade pricing
plans.
