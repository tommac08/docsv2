page_title: Docker registries support overview | Documentation | Shippable
page_description: This sectiond describes Shippable's support for common Docker registries and enabled scenarios
page_keywords: shippable, Docker, Container, docker hub, docker registry, google container registry, gcr

# Overview

Shippable is the world's only CI/CD platform built natively on Docker. Since all your builds run on Docker containers, it gives us a unique ability to support advanced Docker workflows. We're constantly adding to our custom Docker support, so check back often!

One of the most important decisions when using Docker for development is figuring out where to store and manage your Docker images. You can choose to run your own private registry, or pick from a hosted option like [Docker Hub](https://hub.docker.com/account/signup/) or [Google Container Registry](https://cloud.google.com/tools/container-registry/).

Once you've zeroed down on the registry option that fits your needs, you can configure Shippable to pull images from and push images to your registry as part of your CI workflow.

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

## Caching support

In most cases, you can cache your images between builds by turning cache within your project settings.

However, there are some exceptions where caching is not supported. Specifically, *if you are pushing images to Docker Hub or GCR, we do not recommend turning on caching*. It might still work in most cases, but there are some scenarios where caching breaks down and can cause your push to Docker Hub or GCR to fail.

If you run into this issue, you should include [reset_minion] in your commit message to clear cache for that project, and try running your build again.

-------

## Docker Hub

Shippable allows you to interact with Docker Hub in any part of your build workflow. You can pull custom images from your Docker Hub repos, push images to Docker Hub or do a Docker Build.

Check out the [Docker Hub Documentation](https://docs.docker.com/docker-hub/) to learn more.

### Pull images from Docker Hub

1. Follow the instructions to [Integrate your DockerHub account to Shippable](integrations_dockerhub.md).
2. Follow instructions on the [Project Settings Page](project_settings.md) to add the integration to your project.
3. On your Project page, click on the `Settings` tab and set the following options:
  - Pull image from : docker_hub_username/image_name
4. Click on `Save`

_The username above should be the same as the Docker Hub credentials you entered while connecting Docker Hub to Shippable._

-------

## Push images to Docker Hub

1. Follow the instructions to [Integrate your DockerHub account to Shippable](integrations_dockerhub.md).
2. Follow instructions on the [Project Settings Page](project_settings.md) to add the integration to your project.
3. On your Project page, click on the `Settings` tab and set the following options:
   - Push Build : Yes
   - Push image to : docker_hub_username/image_name
   - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags. Check out our [blog](http://blog.shippable.com/immutable-containers-with-version-tags-on-docker-hub) on immutable containers to know why*
4. Click on `Save`

_The username above should be the same as the Docker Hub credentials you entered while connecting Docker Hub to Shippable._

---

## Google Container Registry

Shippable allows you to interact with Google Container Registry(GCR) in any part of the build workflow. You can pull custom images from GCR, push images to GCR or do a Docker build.

Learn more about GCR at [Google's documentation](https://cloud.google.com/tools/container-registry) or [announcement blog](http://googlecloudplatform.blogspot.com/2015/01/secure-hosting-of-private-Docker-repositories-in-Google-Cloud-Platform.html).

### Pull images from GCR

1. Follow the instructions to [Integrate your GCR account with Shippable](integrations_gcr.md).
2. Follow instructions on the [Project Settings Page](project_settings.md) to add the integration to your project.
3. On your Project page, click on the `Settings` tab and set the following option:
  - Pull image from : gcr.io/gcr_project_id/image_name
4. Click on `Save`

-------

### Push images to GCR

1. Follow the instructions to [Integrate your GCR account to Shippable](integrations_gcr.md).
2. Follow instructions on the [Project Settings Page](project_settings.md) to add the integration to your project.
3. On your Project page, click on the `Settings` tab and set the following options:
   - Push Build : Yes
   - Push image to : gcr.io/gcr_project_id/image_name
   - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags. Check out our [blog](http://blog.shippable.com/immutable-containers-with-version-tags-on-docker-hub) on immutable containers to know why*
4. Click on `Save`

>**Note**
>
>Pushing to GCR needs a credit card to be associated with your Google Cloud Platform account. If you do not have that configured in your [Google Dev Console](https://console.developers.google.com), the push will fail since GCR will reject it.

## Docker Build

You can run your build in a custom Docker container by building a Docker image from a Dockerfile. Aside from providing a custom environment for your build, this image created can be pushed to your Docker Hub or GCR account, for later use in your deployment step.

There are 2 ways to set up Docker Build with Shippable - pre-CI or post-CI.

Pre-CI workflow is:

- Build the image using Dockerfile at the root of your repo
- Pull code from GitHub/Bitbucket and test code in the container
- Push container to Docker Hub or GCR

Post-CI workflow is:

- Pull image specified from Docker Hub or GCR (default is minv2)
- Pull code from GitHub/Bitbucket and test in container
- If CI passes, build container from Dockerfile at the root of the repo
- Push container to Docker Hub or GCR

To use these workflows, your app must be "Dockerized". Details on this can be found in Docker's official documentation [Docker's official documentation](https://docs.dockerhub.com). See the [sample projects](#sample-projects) section for an example.

### Pre CI Dockerbuild

1. Enable the repository on Shippable
2. Make sure that Dockerhub/GCR integration is created in your [account settings](account_settings.md) and the integration is enabled in your [project settings](project_settings.md)
3. On the Shippable Project page, go to 'Settings' and set the following options:
    - Docker Build : ON
    - Docker Build Order : Pre-CI
    - Push Build : Toggle Yes/No based on whether you want to push to Dockerhub or GCR
    - Push Image to : dockerhub_username/image_name or gcr.io/gcr_project_id/image_name
      _We need an image name for the image we build from your Dockerfile, even if you choose not to push to a registry_
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.
    - Source Location : (source code location where tests will be run)
4. Make sure the Dockerfile for the image you want to build is at the root of your repo
5. Trigger a manual or webhook build
6. After the build is complete, make sure your repo on Dockerhub or GCR project shows the image you just pushed. The image should be tagged with the Push image tag you set.

### Post CI Dockerbuild

1. Enable the repository on Shippable
2. Make sure that Dockerhub/GCR integration is created in your [account settings](account_settings.md) and the integration is enabled in your [project settings](project_settings.md)
3. On the Shippable Project page, go to 'Settings' and set the following options:
    - Docker Build : ON
    - Dockerbuild order : Post-CI
    - Push Build : Toggle Yes/No based on whether you want to push to Dockerhub or GCR
    - Push Image to : dockerhub_username/image_name or gcr.io/gcr_project_id/image_name
    _We need an image name for the image we build from your Dockerfile, even if you choose not to push to a registry_
    - Push image tag : Click on the dropdown and choose the appropriate setting. You can choose your commitsha as your tag to keep it distinct or set the custom_tag to `latest`. By default, build numbers are used as tags.
    - Pull image from : Since your Dockerbuild is happening post CI, enter the image you want to use for CI
4. Make sure the Dockerfile for the image you want to build is at the root of your repo
5. Trigger a manual or webhook build
6. After the build is complete, make sure your repo on Dockerhub or GCR project shows the image you just pushed. The image should be tagged with the Push image tag you set.

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

## Sample Projects

The [Docker build sample app](https://github.com/shippableSamples/docker-build-nodejs) demonstrates how to dockerize a simple nodejs app.

The [sample project](https://github.com/shippableSamples/sample-gcr) demonstrates a simple scenario - choose a public image from Docker Hub, run CI on that container, and if CI passes, push the resulting image to a private GCR image.
