page_title: Shippable CI/CD Settings| Documentation | Shippable
page_description: How to update Subscription CI/CD Settings
page_keywords: ci settings, add containers, subscription settings, CI/CD, shippable CI, documentation, shippable, config, yml

# CI Subscription Settings

This explains the settings available at your subscription level.

## How do you get here?

- Login to [Shippable](https://shippable.com)
- Click on **CI** on the landing page
- Click on a specific subscription from the dropdown
- Click on the **Settings** tab from the Subscription dashboard

## Sync Subscription

Use this icon to force sync your subscription if you have updated your subscription settings upstream in Github or Bitbucket and you need the update to be propagated immediately.

## Deployment Key

This is the SSH public key associated with your Shippable Account. You will need this key to enable Continuous Deployment for some providers that supports git based deployments. (Heroku and Red Hat Openshift, for example)

See our **How To** section for details on how to enable continuous deployment with different providers.
