page_title: Shippable Project Settings| Documentation | Shippable
page_description: How to configure Shippable CI Project Settings
page_keywords: project settings, CI/CD, shippable CI, documentation, shippable, config, yml, build configuration, build timeout, build badge

# Project Settings

This page documents how and where the configuration required for your build is set up. These are set up at a project level and applies to all builds for that project. Some of these are set up in the `Settings` tab of your project page, while others are configured in your YML.

**NEEDS UPDATE AFTER UI IS COMPLETE**

## Settings from the Project Dashboard

Let's walk through the various options you see when you click on `Settings` on your project page.

### Build Image

Our default image, shippable/minv2, comes installed with popular versions of all
supported languages, tools and services.

However, you might prefer starting with a small image that only has
versions of your language installed. To help with this, we have open
sourced basic images for all supported languages. These images only come
with popular versions of a language and are NOT pre-installed with any
tools, addons or services.

Our build images are available on Docker Hub in the [shippableImages account]
(https://registry.hub.docker.com/repos/shippableimages/) . Dockerfiles
for these images are in our [GitHub repository](https://github.com/shippableImages).

You can choose language specific images for your project by setting the following option under Project Settings -

  **Pull Image from:** This is where you specify the build_image you want Shippable to use as the base image for your project. If nothing is provided, we currently use shippable/minv2 by default. You can pull any public image or private image from Docker Hub or GCR. To pull private images, make sure (1) you follow the [integrations instructions] to integrate your Docker Hub or GCR account with Shippable (2) Activate the integration on the project page by scrolling down to the Integrations section and clicking on `Hub`

```
Example:

dockerhub_username/repo_name: shippableimages/ubuntu1204_nodejs

```

> **Note**
>
> Remember to set your HUB Integration to the correct registry if you are pulling a private repo from Docker Hub or GCR
>

As mentioned above, our language specific images do not come with any tools, addons, or services pre-installed. If you need pre-installed tools, addons or services, then you should use shippable/minv2 image.

Check out our [language help page](languages.md) for language specific info about build images.

### Docker Build

This tells us whether you want to do a Docker build for your project ie., run your build on a custom Docker container by building a Docker image. The default CI mode is **OFF**.

Check out the detailed documentation for [Docker Build](docker_registries/#docker-build) if you turn this to **ON**.

### Push Build Image

**Push Build:** Set this to **ON** if you would like to push your image to a registry after the CI build is complete. We support both Docker Hub and GCR.

**Push image to:** Provide the full path of the registry location where you would like to push the build output.

```
Example:
docker_hub_username/image_name
gcr.io/gcr_project_id/image_name

```

**Push image tag:**  Click on the dropdown and choose the appropriate setting. You can choose between `default`, `commitsha` or `custom` as your tag. For example, you can set your `custom` tag to `latest`.

By default, we use build numbers as tags. Check out our [blog](http://blog.shippable.com/immutable-containers-with-version-tags-on-docker-hub) on immutable containers to know why

### Lighthouse

You will see an option to set [Lighthouse Notification] ON for an image that you push to the registry. If you set this to ON, you will be notified everytime the image gets updated in the registry.

If you do turn ON the lighthouse option, remember to turn on your notification integration for the project as well. (Scroll down to Integrations and turn on Notification Integration)

See our [Lighthouse Page](lighthouse.md) for details on how to use the feature.

### Cache Container

By default, Cache is set to OFF. Turn this on if you want to save build sessions between builds.

Shippable does not cache dependencies between builds by default. Each build will run on a fresh minion and as soon as the build finishes execution, the minion will be deleted. However, we also understand that installing dependencies for each build will take more time and it affects your build speed. Hence the caching feature.

Before the build, we will check for the **cache** flag and if it is ON, the entire minion will be cached if the build succeeds and the cached minion will be reused for further builds.

Caching is done per build host, so it might take a few builds for all
our hosts to get the cached minion. Additional details on caching can be
found on our [blog](http://blog.shippable.com/container-caching) .

#### Reset Cache

You can use the ```[reset_minion]``` tag in your commit message to reset the minion. We will clear all the cached dependencies and packages, when we see a [reset_minion] tag and your build will run on a fresh minion. Once this build finishes execution, we will cache the minion once again so that further builds can run using the cached minion.

### Project Integrations

#### Hub Integration

Choose the registry you want to connect to for this project from the dropdown. You can choose between Docker Hub and Google Container Registry at this point.

#### Notification Integration

Shippable primarily supports email and irc notifications and these can
can be configured in your yml file. To send Slack notifications, please
check out our [blog post](http://blog.shippable.com/devops-chat-a-simple-way-to-use-slack-notifications-with-shippable).
To send HipChat notifications, check out our [sample project for hipchat notifications](https://github.com/shippableSamples/sample-hipchat-notifications).

By default, we send email notifications to the last committer when a
build fails, or the status changes from failed to passed.

You can change the default settings for email notifications by
configuring the notifications section of your yml. You can specify the
email address(es) where you want to receive notification as well as the
criteria for when you want notifications to be sent.

#### Email notifications

To send notifications to specific email addresses, replace the sample
email addresses below with the recipients' email ids in your
`shippable.yml` file.

```yaml
notifications:
    email:
        - exampleone@org.com
        - exampletwo@org.com
```

You can also specify when you want to get notified by setting the values
for on_success and on_failure keys to change|always|never. Change
means you want to be notified only when the build status changes on the
given branch. Always and never mean you want to be notified always or
never respectively.

```yaml
notifications:
     email:
         recipients:
             - exampleone@org.com
             - exampletwo@org.com
         on_success: change
         on_failure: always
```

If you do not want to get notified for any reason, you can configure
email notifications to false.

```yaml
notifications:
   email: false
```

#### IRC notifications

You can also configure yml file to send build notifications to your IRC
channels.

- To specify single channel

```yaml
notifications:
   irc:  "chat.freenode.net#channel1"
```

- You can also specify multiple server channels in yml file. The
  following formats are supported:

```yaml
notifications:
  irc:
    - "chat.freenode.net#channel1"
    - "chat.freenode.net#channel2"
    - "server1#channel3"
```

```yaml
notifications:
  irc:
   channels:
     - "chat.freenode.net#channel1"
     - "chat.freenode.net#channel2"
     - "server1#channel3"
```

- By default, We will always send build notifications to the mentioned
  channels in yml. **on_success** and **on_failure** are not yet
  configurable.
- IRC notifications are turned off by default for pull request builds.
  However, you can change the default settings by adding
  **pull_requests: true** tag in your yml as shown below.

```yaml
notifications:
  irc:
   pull_requests: true
   channels:
```

---

### Encrypt Environment Variables

Shippable allows you to encrypt the environment variable definitions and
keep your configurations private using **secure** tag. Go to the org
dashboard or individual dashboard page from where you have enabled your
project and click on **ENCRYPT ENV VARIABLE** button on the top right
corner of the page. Enter the env variable and its value in the text box
as shown below.

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

