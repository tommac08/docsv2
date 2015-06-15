page_title: Docker Build  | Documentation | Shippable
page_description: How to do a Docker Build using Shippable
page_keywords: shippable, Docker, Container, docker hub, docker registry, google container registry, gcr

# Docker Build

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

To use these workflows, your app must be "Dockerized". Details on this can be found in Docker's official documentation [Docker's official documentation](https://docs.dockerhub.com).

A sample project can be found [here](https://github.com/shippableSamples/docker-build-nodejs) that demonstrates how to dockerize a simple nodejs app.


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

