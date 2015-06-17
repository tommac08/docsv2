page_title: Shippable Project Settings| Documentation | Shippable
page_description: How to configure Shippable CI Project Settings
page_keywords: project settings, CI/CD, shippable CI, documentation, shippable, config, yml, build configuration, build timeout, build badge

# Project Settings

This page documents the configuration for your build that is set up in the UI. These apply to *all builds* for that project.

Check out the [YML Reference](yml_reference) and the [How To Run a Build](build_case2) for additional settings that need to be configured via the YML.

## How do you get here?

- Login to [Shippable](https://shippable.com)
- Click on **CI** on the Shippable Landing Page and choose the appropriate subscription
- This brings you to the [Subscriptions Dashboard](ci_dashboard). If your project is enabled, it will show up on the dashboard. If not, click on the ![add icon](images/enable_icon.gif) to enable your project
- Click on the project
- Select the `Settings` tab

## Project Settings

### Docker Build

This tells us whether you want to do a Docker build for your project ie., run your build on a custom Docker container by building a Docker image. The default CI mode is **OFF**.

Read our guides on [How to do a Docker Build](docker-build) to learn more.

### Build Image

**Pull Image from:** This is where you specify the build_image you want Shippable to use as the base image for your project. If nothing is provided, we use shippable/minv2 by default.

Example: `dockerhub_username/repo_name: shippableimages/ubuntu1204_nodejs`

You can pull any public image or private image from Docker Hub or GCR. Read our guide on [how to use Docker Registries](docker_registries.md) to learn more about pulling images from Docker Hub or GCR.

The default image, shippable/minv2, comes installed with popular versions of all
supported languages, tools and services. However, you might prefer starting with a small image that only has versions of your language installed (and we highly recommend this). To help with this, we have open sourced basic images for all supported languages. These images only come with popular versions of a language and are NOT pre-installed with any
tools, addons or services.

Check out our [language help page](languages.md) for language specific info about build images.

Our build images are available on Docker Hub in the [shippableImages account]
(https://registry.hub.docker.com/repos/shippableimages/) . Dockerfiles
for these images are in our [GitHub repository](https://github.com/shippableImages).

> **Note**
>
> Don't forget to set your HUB Integration to the correct registry if you are pulling a private repo from Docker Hub or GCR. Read the [Project Integrations section](project_settings/#project-integrations) to learn how.
>

### Push Build Image

**Push Build:** Set this to **Yes** if you would like to push your image to a registry after the CI build is complete. We support both Docker Hub and GCR.

**Push image to:** Provide the full path of the registry location where you would like to push the build output.

Example:
`docker_hub_username/image_name`
`gcr.io/gcr_project_id/image_name`

**Push image tag:**  Click on the dropdown and choose the appropriate setting. You can choose between `default`, `commitsha` or `custom` as your tag. For example, you can set your `custom` tag to `latest`.

By default, we use build numbers as tags. Check out our [blog](http://blog.shippable.com/immutable-containers-with-version-tags-on-docker-hub) on immutable containers to know why

More details [here](docker_registries.md).

### Lighthouse

You will see an option to turn ON Lighthouse for an image that you push to the registry. (Push Build = YES). If you set this to ON, you will be notified everytime the image gets updated in the registry.

When you turn ON the lighthouse option, remember to select the notification integration for the project. Scroll down to Project Integrations and select the appropriate Notification Integration.

See our [Lighthouse Page](lighthouse.md) for details on how to use the feature.

Read [how to set up a Notification Integration](integrations.md)


### Cache Container

By default, Cache is set to OFF. Turn this on if you want to save build sessions between builds.

Shippable does not cache dependencies between builds by default. Each build will run on a fresh minion and as soon as the build finishes execution, the minion will be deleted. However, we also understand that installing dependencies for each build will take more time and it affects your build speed. Hence the caching feature.

Before the build, we will check for the **cache** flag and if it is ON, the entire minion will be cached if the build succeeds and the cached minion will be reused for further builds.

Caching is done per build host, so it might take a few builds for all
our hosts to get the cached minion. Additional details on caching can be
found on our [blog](http://blog.shippable.com/container-caching) .

#### Reset Cache

You can use the ```[reset_minion]``` tag in your commit message to reset the minion. We will clear all the cached dependencies and packages, when we see a [reset_minion] tag and your build will run on a fresh minion. Once this build finishes execution, we will cache the minion once again so that further builds can run using the cached minion.

## Project Integrations

### Hub Integration

You will need this if you are pulling from or pushing to a docker registry, such as Docker Hub or Google Container Registry.

PRE-REQUISITE: Before setting an integration at the project level, you need to connect the registry at an account level. The [Integrations guide](integrations.md) tells you how to create account level integrations that you can use across projects.

When you click on the Hub Button, you will see the list of integrations that are available for you to use. This may also include integrations created by owners in other subscriptions you belong to.

Choose the appropriate integration name to connect to the registry of your choice.

### Notification Integration

This integration is currently used to tell the project the notification integration you want to use for Lighthouse if you want to be notified of changes in the image you are pushing.

The [Integrations guide](integrations.md) will tell you how to create the notification integration at the account level.

>**Note:
>If there are no integrations created at the account level, you will not see any options when clicking the dropdown on either HUB or NOTIFICATION

---

## Encrypt Environment Variables

Shippable allows you to encrypt the environment variable definitions and
keep your configurations private using **secure** tag.

Enter the env variable and its value in the text box as shown below.

```
name=abc
```

Click on the encrypt button and copy the encrypted output string and add
it to your yml file as shown below:

```yaml
env:
  secure: <encrypted output>
```

To encrypt multiple environment variables and use them as part of a
single build, enter the environment variable definitions in the text box
as shown below

```
name1="abc" name2="xyz"
```

This will give you a single encrypted output that you can embed in your
yml file.

You can also combine encrypted output and clear text environments using
**global** tag.

```yaml
env:
  global:
    - FOO="bar"
    - secure: <encrypted output>
```

To encrypt multiple environment variables separately, configure your yml
file as shown below:

```yaml
env:
  global:
    #encrypted output of first env variable
    - secure: <encrypted output>
    #encrypted output of second env variable
    - secure: <encrypted output>
  matrix:
    #encrypted output of third env variable
    - secure: <encrypted output>
```

> **Note**
>
> Due to the security risk of exposing your secure variables, we do not
> decrypt secure variables for pull request from the forks of public
> projects. Secure variable decryption is limited to the pull request
> triggered from the branches on the same repository. And the decrypted
> secured variables are also not displayed in the script tab for
> security reasons.

------

## YML Settings

Please read the [detailed reference](yml_reference.md) of the YML settings that you can use to configure your build.


## Run a Build

Read our guide on [how to run a build](build_case2) to learn how to use these settings within the context of running a build.

