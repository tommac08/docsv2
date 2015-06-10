page_title: Sign Up to Shippable CI or Formations
page_description: Getting Started - Sign up
page_keywords: getting started, questions, documentation, sign up, shippable

# Sign up for Shippable CI or Formations

## **Sign in to Shippable**

You can sign in to Shippable using a GitHub or Bitbucket account. We use
OAuth authentication, so you do not need to create a separate account on
our platform.

To sign in, visit the [Shippable website](https://www.shippable.com),
click **Login**, and choose between Github or Bitbucket auth.

## **Allow Access to repos**

After entering your credentials in the previous step, you will be prompted to give Shippable
access to your repos. GitHub and Bitbucker auth behave a little
differently as follows -

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

By default, you will be signed up to our Free CI Plan that allows you to build and test with Shippable CI with one build container!

## **Add a Formation**

- Login to [Shippable](https://www.shippable.com)
- On the Dashboard, Select "Add a Formation"
- [[UPDATE AFTER UX is done]] This will take you to the "New Plan Addition" Page, where you can choose the number of containers you would like to buy for your Formation Subscription

## **Upgrade your Shippable CI Plan**

- Login to Shippable
- On the dropdown, select the "Subscription" you would like to upgrade
- This will take you to the Subscriptions Dashboard. Click on `Settings` on the right side bar.
- [[UPDATE AFTER UX is done]] Here you can choose to increase the number of containers for your Shippable CI plan


