page_title: Docker registries support overview
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
