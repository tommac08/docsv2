page_title: Custom Containers with Docker Build
page_description: Running minions in a Docker container defined by a Dockerfile
page_keywords: shippable, Docker, Container

# Docker Hub

[Docker Hub](https://hub.docker.com/account/signup/) is a hosted registry that provides public and private Docker image storage.

Shippable allows you to interact with Docker Hub in any part of your build workflow. You can pull custom images from your Docker Hub repos or push images to Docker Hub. This is a 3-step process.

 1. Connect your Docker Hub account to your Shippable Account
 2. Add the Docker Hub Integration to your project
 3. Update your Project Settings to pull from or push to the specific image
  
Read on for detailed instructions -

## Connect your Docker Hub account to Shippable

- Login to Shippable
- Click on `Settings` in the top navbar
- On the Account Settings page, click on `Integrations`
- From the options presented, click on `Docker Hub Credentials`
- Enter an Integration name, which will be used to refer to this integration on Shippable
- Enter your credentials 
- Click on `Save`

![screen shot 2015-05-28 at 10 24 56 am](https://cloud.githubusercontent.com/assets/9526532/7866524/f1c11748-0524-11e5-95ea-b5d3fc5b30a6.png)

Once the Docker Hub Integration is created at an Account level, you are ready to configure your project to pull from or push images to Docker Hub. 

-------

## Add Docker Hub Integration to your project

- Go to your repository page on Shippable
- Click on the drop down on the right panel called `Integrations`
- Choose the appropriate Docker Hub Integration from the dropdown. If you don't see any, please follow the instructions in the previous section to Connect a Docker Hub Account to your Shippable Account.

---------

## Pull images from Docker Hub

- On the repository page on Shippable, go to the 'Settings' tab
- Choose the following to pull an image from Docker Hub -
    - Pull image from : docker_hub_username/image_name
- You can also pull a public image from Shippable's list of images from the image list dropdown
- Click on `Save`

The username above should be the same as the Docker Hub credentials you entered while connecting Docker Hub to Shippable.

-------

## Push images to Docker Hub

- On the repository page, go to 'Settings'.
- Choose the following to push to Docker Hub -
    - Push Build : Yes
    - Push image to : docker_hub_username/image_name
- Click on `Save`

The username above should be the same as the Docker Hub credentials you entered while connecting Docker Hub to Shippable.

---

## Docker Build

> **Note**
>
> Docker Build Support is only available with dedicated hosts. To set up
> a dedicated host, please follow instructions
> [here](config.md#dedicated-hosts)

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

### Pre-CI Dockerbuild

- Make sure your Shippable org is connected to your Docker Hub account
- Enable the repository on Shippable
- On the repo page, go to 'Settings'. Choose the following:
    - Build image : Custom Image
    - Custom image action : Build
    - Custom image name : (docker hub username)/(image name)
    - Source code path : (source code path for image you want to build)
    - Push to Docker Hub : Check
- Make sure the Dockerfile for the image you want to build is at the root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your Docker Hub account has
  the image you just pushed. The image should be tagged with the build
  number on Shippable.

### Post-CI Dockerbuild

- Make sure your Shippable org is connected to your Docker Hub account
- Enable the repository on Shippable
- On the repo page, go to 'Settings'. Choose the following -
    - Build image : Custom Image
    - Custom image action : Build
    - Custom image name : (docker hub username)/(image name)
    - Source code path : (source code path for image you want to
      build)
    - Docker build when finished : Check
    - Image to pull: Specify image you want to run tests on, default
      is shippable/minv2
    -  Push to Docker Hub : Check
- Make sure the Dockerfile for the image you want to build is at the
  root of your repo
- Trigger a manual or webhook build
- After the build is complete, make sure your Docker Hub account has
  the right image. The image should be tagged with the build number on
  Shippable.

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
