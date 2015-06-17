page_title: Shippable Formations Overview | Documentation | Shippable
page_description: Overview of Shippable Formations
page_keywords: formations,multi containers, microservices, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

Shippable Formations gives developers an easy way to provision, configure, and deploy multi-container Dev and Test clouds (environments) with zero DevOps code. You can create full-topology environments that can mimic your production environment or represent any combination of software component versions you'd like to deploy.

This page walks through how to use the rest of the guide in the Shippable Formations section.

## How to use the Shippable Formations user guide

The user guide is organized by the different tabs on the product - so you will find a page for each tab under **Shippable Formations**

## Add a new formation

- Login to [Shippable](https://shippable.com)
- On the Landing page, Click on Formations

     ![Shippable Formations](images/formations_landing.gif)

- Choose `Add Formation`
- On the Subscription Plan Management Page, start with a name for your formation. For example: `Develop-1`
- Select the number of minions you want for your formation. As a guideline, we recommend one minion for every image in your application.
- Enter your Billing Address
- If you don't have any Payment Instruments stored, Clicking on Payment Method will take you to the [cards](payment_methods.md) section where you can enter your payment details.
- Once you are done, come back to the Billing Tab to complete your transaction.
- Click on the `Buy` button and you are ready to use your new formation

## Formation Status

To learn how to manage your formations by different environments, Go to [Formations Status](formations_status.md)

A formation is comprised of images, configuration variables and environments. Once these are set up in your formation, you will be ready to do one-click deployments and rollbacks across your environments.

## Formation Settings

Go to [Formations Settings](formation_settings.md) to learn how to set up a formation, manage your applications and team permissions.

## Formation Billing

Go to [Formations Billing](formations_billing.md) to learn how to add a new formation or edit the number of containers in your existing formation.


