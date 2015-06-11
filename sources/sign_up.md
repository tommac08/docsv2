page_title: Sign Up to Shippable | Documentation | Shippable
page_description: Getting Started - Sign up
page_keywords: getting started, questions, documentation, sign up, shippable

# Sign up for Shippable

## Prerequisite

Shippable uses either your Github or Bitbucket account to authenticate. You must have your source code in one of these two repos to sign up for Shippable

[Github](https://github.com)
[Bitbucket](https://bitbucket.org)

## Sign in to Shippable

Since we use OAuth authentication, if you have either a Github or Bitbucket account, you do not need to create a separate account on our platform.

To sign in, visit the [Shippable website](https://www.shippable.com),
click **Login**, and choose between Github or Bitbucket auth. This will take you to either the Github or Bitbucket Sign In page, where you enter your credentials.

## Allow Access to repos

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

## Choose what you want to do

Every user is signed up to our **Free CI Plan** by default. This allows you to run one build concurrently on Shippable CI.

When you get to the landing page, on the drop down, you will see three sections on the dropdown -

**Section 1: CI Subscriptions**

- You may see one or many subscriptions in this section, if you are collaborating on multiple projects. Each subscription maps to a Github or Bitbucket Account.

- If you want to continue onto doing Shippable CI, choose the appropriate subscription from the section.

**Section 2: Add Formation**

For more details on Formations, go to [Formations Overview](formations_overview.md)

**Section 3: Lighthouse**

For more details on Lighthouse, go to [Using Lighthouse](lighthouse.md)
