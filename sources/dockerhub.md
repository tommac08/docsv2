page_title: Custom Containers with Docker Build | Documentation | Shippable
page_description: Running minions in a Docker container defined by a Dockerfile
page_keywords: shippable, Docker, Container

# Docker Hub

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
