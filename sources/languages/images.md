page_title: Build images
page_description: A brief description about how to use language specific docker images
page_keywords: custom images, dedicated hosts, shippable images, language specific docker images

# Build images

Our default image, minv2, comes installed with popular versions of all
supported languages, tools and services. This is the image that is used
for your builds if nothing is specified in your shippable.yml with a **build_image**
tag.

However, you might prefer starting with a small image that only has
versions of your language installed. To help with this, we have open
sourced basic images for all supported languages. These images only come
with popular versions of a language and are NOT pre-installed with any
tools, addons or services. You will have to customize your shippable.yml
file to install these based on the project requirements. You can then
enable caching to make sure your pre-requisites are not installed for
each build.

Our build images are available on Docker Hub in the [shippableImages account]
(https://registry.hub.docker.com/repos/shippableimages/) . Dockerfiles
for these images are in our [GitHub repository](https://github.com/shippableImages) .

The syntax to use language specific images is:

```yaml
build_image: <docker_hub_username>/<image_name>
```

As mentioned above, our language specific images do not come with any
tools, addons, or services pre-installed. If you need pre-installed
tools, addons or services, then you should use shippable/minv2 image.

> **Note**
>
> If you want to run builds using your own custom images, then you will
> have to enable [Dedicated hosts](http://blog.shippable.com/dedicated-hosts-) You will need the **build_image** tag in shippable.yml file with the path of the image.

The section will give you more details on specific images.

---
