page_title: Shippable CI/CD Billing| Documentation | Shippable
page_description: How to update Subscription CICD Plan and add more containers
page_keywords: ci billing, add containers, subscription settings, CI/CD, shippable CI, documentation, shippable, config, yml

# CI/CD Subscription Billing

This page explains how to update your CI/CD plan from the Billing Tab.

****ADD SCREENSHOT****

## How do you get here?

- Login to Shippable
- Click on **CI/CD** on the landing page
- Click on a specific subscription from the dropdown
- Click on the **Billing** tab from the Subscription dashboard

## Subscription Billing

This is where you can manage your subscription plan and edit your number of containers.

- Make sure your plan is set to `Multi-Tenant CI/CD`
- Use the slider to select the number of containers you want for your subscription.
- The total price automatically updates to reflect the price you will pay for the number of containers chosen
- Enter your payment details
- Click the **Buy** button
- Your CI/CD Plan is now updated and the number of minion count on your Subscriptions dashboard should reflect the new number of containers on your plan

## Invoices

You can view or download your past invoices in this section. Click on `download` to download any invoice to your local computer.

## Sync Subscription

Use this icon to force sync your subscription if you have updated your subscription settings upstream in Github or Bitbucket and you need the update to be propagated immediately.

## Deployment Key

This is the SSH public key associated with your Shippable Account. You will need this key to enable Continuous Deployment for some providers that supports git based deployments. (Heroku and Red Hat Openshift, for example)

See our **How To** section for details on how to enable continuous deployment with different providers.
