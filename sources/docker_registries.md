page_title: Docker registries support overview | Documentation | Shippable
page_description: This sectiond describes Shippable's support for common Docker registries and enabled scenarios
page_keywords: shippable, Docker, Container, docker hub, docker registry, google container registry

# Overview

Shippable is the world's only CI/CD platform built natively on Docker. Since all your builds run on Docker containers, it gives us a unique ability to support advanced Docker workflows. We're constantly adding to our custom Docker support, so check back often!

One of the most important decisions when using Docker for development is figuring out where to store and manage your Docker images. You can choose to run your own private registry, or pick from a hosted option like [Docker Hub](https://hub.docker.com/account/signup/) or [Google Container Registry](https://cloud.google.com/tools/container-registry/).

Once you've zeroed down on the registry option that fits your needs, you can configure Shippable to pull images from and push images to your registry as part of your CI workflow. We currently support the two most popular hosted options - Docker Hub and Google Container Registry.

We're always adding support for additional registries, so check back often or let us know what you're looking for by opening a [support issue](https://github.com/Shippable/support/issues).

## Scenarios

The following is a matrix of supported and unsupported scenarios for our image registry integration feature -

| Pull from                       | Push to                     | Support                  |
| --------------------------------- |---------------------------------| ------------------------ |
| public image on Docker Hub      | private image in Docker Hub | Supported with all plans |
| public image on Docker Hub      | private image in GCR        | Supported with all plans |
| private image from Docker Hub   | private image in Docker Hub | Supported with all plans |
| private image from GCR          | private image in GCR        | Supported with all plans |
| private image from Docker Hub   | private image in GCR        | Unsupported |
| private image from GCR          | private image in Docker Hub | Unsupported |

 For further details on how to push to and pull from Docker Hub, read our [Docker Hub integration](docker.md) documentation.

 For further details on how to push to and pull from GCR, read our [GCR integration](gcr.md) documentation.

You should also check out our [sample project](https://github.com/shippableSamples/sample-gcr) which demonstrates a simple scenario - choose a public image from Docker Hub, run CI on that container, and if CI passes, push the resulting image to a private GCR image.

## Caching support

In most cases, you can cache your images between builds by turning cache within your project settings.

However, there are some exceptions where caching is not supported. Specifically, *if you are pushing images to Docker Hub or GCR, we do not recommend turning on caching*. It might still work in most cases, but there are some scenarios where caching breaks down and can cause your push to Docker Hub or GCR to fail.

If you run into this issue, you should include [reset_minion] in your commit message to clear cache for that project, and try running your build again.

[Docker Hub](https://hub.docker.com/account/signup/) is a hosted registry that provides public and private Docker image storage.

Shippable allows you to interact with Docker Hub in any part of your build workflow. You can pull custom images from your Docker Hub repos or push images to Docker Hub. This is a 3-step process.

 1. Connect your Docker Hub account to your Shippable Account
 2. Add the Docker Hub Integration to your project
 3. Update your Project Settings to pull from or push to Docker Hub

Read on for detailed instructions -

## Connect your Docker Hub account to Shippable

Follow the instructions from the [Account Settings Page] (account_settings/#connect-to-docker-hub) to set up your Dockerhub Integration

At this point, you have set up Dockerhub integration at an account level. To push and pull from Dockerhub, you will also need to enable repo-level access as described in the scenarios below.

-------

## Add Docker Hub Integration to your project

Follow instructions [here](project_settings.md) to add a Dockerhub integration to your project

---------

## Pull images from Docker Hub

- On the repository page on Shippable, go to the 'Settings' tab
- Choose the following to pull an image from Docker Hub -
    - Pull image from : docker_hub_username/image_name
- Click on `Save`

The username above should be the same as the Docker Hub credentials you entered while connecting Docker Hub to Shippable.

-------

## Push images to Docker Hub

- On the repository page, go to 'Settings'.
- Choose the following to push to Docker Hub -
    - Push Build : Yes
    - Push image to : docker_hub_username/image_name
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags. Check out our [blog](http://blog.shippable.com/immutable-containers-with-version-tags-on-docker-hub) on immutable containers to know why*
- Click on `Save`

The username above should be the same as the Docker Hub credentials you entered while connecting Docker Hub to Shippable.

---

## Docker Build

You can run your build in a custom Docker container by building a Docker
image from a Dockerfile. Aside from providing a custom environment for
your build, this image created can be pushed to your Docker Hub account,
for later use in your deployment step.

There are 2 ways to set up Docker Build with Shippable - pre-CI or post-CI.

Pre-CI workflow is:

- Build the image using Dockerfile at the root of your repo
- Pull code from GitHub/Bitbucket and test code in the container
- Push container to Docker Hub

Post-CI workflow is:

- Pull image specified from Docker Hub (default is minv2)
- Pull code from GitHub/Bitbucket and test in container
- If CI passes, build container from Dockerfile at the root of the repo
- Push container to docker hub

To use these workflows, your app must be "Dockerized". Details on this
can be found in Docker's official documentation [Docker's official
documentation](https://docs.dockerhub.com). You can also look at our
[Docker build sample app](https://github.com/cadbot/dockerized-nodejs).

### Pre CI Dockerbuild

- Enable the repository on Shippable
- Make sure that Dockerhub integration is created in your Account settings and that Dockerhub Integration is enabled for your repo
- On the repo page, go to 'Settings'. Choose the following -
    - Docker Build : ON
    - Docker Build Order : Pre-CI
    - Push Build : Yes if you want to push to Dockerhub, No if you don't want to push to Dockerhub
    - Push Image to : (dockerhub_username))/(image_name)
      _We need an image name for the image we build from your Dockerfile, even if you choose not to push to Dockerhub_
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.
    - Source Location : (source code location where tests will be run)
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your repo on Dockerhub shows the image you just pushed. The image should be tagged with the build number on Shippable.

### Post CI Dockerbuild

- Enable the repository on Shippable
- Make sure that Dockerhub integration is created in your Account settings and that Dockerhub Integration is enabled for your repo
- On the repo page, go to 'Settings'. Choose the following -
    - Docker Build : ON
    - Dockerbuild order : Post-CI
    - Push Build : Yes if you want to push to Dockerhub, No if you don't want to push to Dockerhub
    - Push image to :(dockerhub_username))/(image_name)
    _We need an image name for the image we build from your Dockerfile, even if you choose not to push to Dockerhub_
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.
    - Pull image from : Since your Dockerbuild is happening post CI, enter the image you want to use for CI
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your repo on Dockerhub shows the image you just pushed. The image should be tagged with the build number on Shippable.

### Copying artifacts to prod image

If you are following the post-CI Docker Build workflow and want to copy
some build artifacts to your prod image, you should:

- Create a shippable/buildoutput directory in your shippable.yml

```bash
before_script:
  - mkdir -p shippable/buildoutput
```

- In the after_script section, copy whatever you want to this
   directory

```bash
after_script:
  - cp -r (your artifacts) ./shippable/buildoutput
```

- In your Dockerfile, you can now use ADD to put the artifacts
   wherever you want in your prod image

```bash
ADD ./buildoutput/(artifacts file) (target)
```

And that's it. Any artifacts you need will be available in your prod
image.


# Google Container Registry

The Google Container Registry (GCR) provides secure, private Docker image storage on the Google Cloud Platform. GCR has additional benefits such as fine grained access control, server-side encryption of images, and super fast deployment to Google Container Engine and Google Compute Engine.

To read more about GCR, you can read [Google's documentation](https://cloud.google.com/tools/container-registry) or [announcement blog](http://googlecloudplatform.blogspot.com/2015/01/secure-hosting-of-private-Docker-repositories-in-Google-Cloud-Platform.html).

## Setting up GCR integration on Shippable

If you want to interact with GCR in any part of your build workflow for your Shippable project, such as using your private images for your builds or pushing images to your repository, you need to connect your GCR project to your Shippable account.

Follow the following steps to set up GCR integration.

#### Create a Project in Google Dev Console

To use Shippable with GCR, you will need a project created using the Google Developers Console (GDC). According to [Google's documentation](https://developers.google.com/console/help/new/#managingprojects) - A project is a collection of settings, credentials, and metadata about the application or applications you're working on that make use of Google Developer APIs and Google Cloud resources.

If you already have a project you want to use, skip to the next step - *Setting up OAuth for your GDC project*.

To create a project -

- Sign in to the [Google Developers Console](https://console.developers.google.com/)
- Click on 'Create Project'
- Enter a name and project ID or accept the defaults.
- Click 'Create'

#### Connect your GCR Account to Shippable

Follow the instructions from the [Account Settings Page] (account_settings/#connect-to-gcr-google-container-registry) to set up your GCR Integration

At this point, you have set up GCR integration at an account level. To push and pull from GCR, you will also need to enable repo-level access as described in the scenarios below.

#### Add GCR Integration to your project

-------

## Pull custom image from GCR

Shippable allows you to pull a custom image from GCR to run your builds on.

To enable GCR integration for the repository for which you want to pull a custom image -

- Go to your repository page on Shippable
- Make sure you have set up the GCR integration for the project as per instructions [here](project_settings.md)
- Go to the `Settings` for the project and set the following option to pull an image from GCR -
    - Pull image from : gcr.io/gcr_project_id/image_name


-------

## Push to GCR

Shippable allows you to push an image to GCR after a successful build.

To enable GCR integration for the repository for which you want to push to GCR -

- Go to your repository page on Shippable
- Make sure you have set up the GCR integration for the project as per instructions [here](project_settings.md)
- Go to the `Settings` for the project and set the following options to pull an image from GCR -
    - Push Build : Yes
    - Push image to : gcr.io/gcr_project_id/image_name
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.

*Note* - Pushing to GCR needs a credit card to be associated with your Google Cloud Platform account. If you do not have that configured in your [Google Dev Console](https://console.developers.google.com), the push will fail since GCR will reject it.

-------

## Build image from Dockerfile

You can run your build in a custom docker container by building a Docker image from a Dockerfile. Aside from providing a custom environment for your build, this image created can be pushed to your GDC account, for later use in your deployment step.

There are 2 ways to set up Docker build with Shippable - pre CI or post CI.

Pre CI workflow is:

- Build the image using Dockerfile at the root of your repo
- Pull code from GitHub/Bitbucket and test code in the container
- Push container to GCR

Post CI workflow is:

- Pull image specified from GCR (default is shippable/minv2)
- Pull code from GitHub/Bitbucket and test in container
- If CI passes, build container from Dockerfile at the root of the repo
- Push container to GCR

To use these workflows, your app must be "dockerized". Details on this can be found in Docker's official documentation [Docker's official documentation](https://docs.dockerhub.com). You can also look at our [Docker build sample app](https://github.com/cadbot/dockerized-nodejs).

### Pre CI Dockerbuild

- Enable the repository on Shippable
- Make sure you have set up the GCR integration for the project as per instructions [here](project_settings.md)
- On the repo page, go to 'Settings'. Choose the following -
    - Docker Build : ON
    - Docker Build Order : Pre-CI
    - Push Build : Yes if you want to push to GCR, No if you don't want to push to GCR
    - Push Image to : gcr.io/(project id on GDC)/(image name)
      We need an image name for the image we build from your Dockerfile, even if you choose not to push to GCR
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.
    - Source Location : (source code location where tests will be run)
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your GDC account shows the image you just pushed. The image should be tagged with the build number on Shippable.

### Post CI Dockerbuild

- Enable the repository on Shippable
- Make sure you have set up the GCR integration for the project as per instructions [here](project_settings.md)
- On the repo page, go to 'Settings'. Choose the following -
    - Docker Build : ON
    - Dockerbuild order : Post-CI
    - Push Build : Yes if you want to push to GCR, No if you don't want to push to GCR
    - Push image to : gcr.io/(project id on GDC)/(image name)
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.
    - Pull image from : Since your Dockerbuild is happening post CI, enter the image you want to use for CI
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your GDC account shows the image you just pushed. The image should be tagged with the build number on Shippable.

### Copying artifacts to prod image

If you are following the post-CI Dockerbuild workflow and  want to copy some build artifacts to your prod image, you should-

1. Create a shippable/buildoutput directory in your shippable.yml

```bash
before_script:
    - mkdir -p shippable/buildoutput
```

2. In the after_script section, copy whatever you want to this directory

```bash
  after_script:
    - cp -r (your artifacts) ./shippable/buildoutput
```

3. In your Dockerfile, you can now use ADD to put the artifacts wherever you want in your prod image

```bash
  ADD ./buildoutput/(artifacts file) (target)
```

And that's it. Any artifacts you need will be available in your prod image.

