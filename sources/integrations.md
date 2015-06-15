page_title: Shippable Integrations | Documentation | Shippable
page_description: Integratons with Dockerhub, GCR, lighthouse
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation, Tokens, account settings, integrations, docker hub, gcr, lighthouse

# Integrations

This page describes the different integrations you can set up for your account. These are set up once for each type of integration and can be re-used in different parts of the Shippable pipeline.

------

## Docker Hub

You will need this to pull or push images to Docker Hub as part of building your project.

1. Click on Account Settings (the gear icon on the top bar) and scroll down to the `Integrations` section
2. To add a new integration, click on (+)
3. **Master Integration:** Click on `Docker`
4. Enter a name for your integration. Use a distinctive name that's easy to associate to the integration and recall. `Example:docker-mydockerusername`
5. Enter your credentials
6. Click on `Save`

Go [here](docker_registries) to learn more about Docker Registries.

--------

## Google Container Registry (GCR)

You will need this to pull or push images to Google Container Registry as part of building your project.

1. On the [Google Developers Console](https://console.developers.google.com/), select the project you want to integrate with Shippable
2. In the sidebar on the left, expand 'APIs & auth' and select 'Credentials'
3. Click 'Create new Client ID' and select 'Service Account' in the pop-up window
4. Click on 'Create Client ID'. A dialog box appears. To proceed, click 'Okay, got it'
5. Your new Public/Private key pair is generated and downloaded to your machine. Please store this carefully since you will not be able to retrieve this from your GDC account.
6. Now you are ready to integrate with GCR on Shippable. Back to [Shippable](https://shippable.com).
7. Click on Account Settings (the gear icon on the top bar) and scroll down to `Integrations`
8. To add a new integration, click on (+)
9. **Master Integration:** Click on `GCR`
10. Enter a name for your integration. Use a distinctive name that's easy to associate to the integration and recall. `Example:gcr-gcrusername`
11. Enter your JSON key that you saved earlier
12. Click on `Save`

Go [here](docker_registries) to learn more about Docker Registries.

------------

## Lighthouse Notification

This is the integration that is used to set up email notifications for Lighthouse, our image monitor. To learn more about lighthouse, go [here](lighthouse.md)

1. Click on Account Settings (the gear icon on the top bar) and scroll down to the `Integrations` section
2. To add a new integration, click on (+)
3. **Master Integration:** Click on `Email Notification`
4. Enter a name for your integration. Use a distinctive name that's easy to associate to the integration and recall. Example: `Example: my-shippable-emailid`
5. Enter your email address: `janedoe@shippable.com`
6. Click on `Save`
