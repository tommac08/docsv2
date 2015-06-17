page_title: Shippable Formations Settings| Documentation | Shippable
page_description: Shippable Formation Settings
page_keywords: formations, formation settings, multi containers, microservices, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

The Formation Settings page is where you can add the different components that your formation is comprised of. A Formation consists of images and config variables as the base components. You can combine these in any way to create an **Service**. The **Service** is deployed to an environment.

Now you are ready to configure an _instance_ of the Service.

Thia happens in the service instance page where you add the versions of the images, values of the config variables and the ports you want exposed, if any. You also set your flag for `auto-deploy` if you want the application re-deployed every time an image is updated in your Application Template.

Once the Service Instance has been updated, you can click `Deploy Service` to deploy the service to the environment. You can see the streaming live logs on the Service Status page.The application will be available at the IP address and port that is displayed on the Status page.

## How do you get here?

- Login to [Shippable](https://shippable.com)
- On the Landing page, click on **Formations**
- Select your Formation
- Click on the `Settings` tab

## Formation Components

The Formation Settings page is where you can add the different components that your formation is comprised of. A Formation consists of images and config variables as the base components. You can combine these in any way to create a **Service**. The **Service** is deployed to an environment.

### Add Images

Add images by clicking on ![add icon](images/add_icon.gif) against Images.

Name: Enter the full reponame. Example: `dockerhubusername/reponame`
Docker Integration: Enter the Integration Name that you set up under [account settings](account_settings.md) to connect to your registry
Port: Enter the port your image is listening on. You can enter multiple ports here separated by comma.

Click on the **Save** icon.

**Tags** The image is synced at that point and you will see the total number of tags or versions available for that image in the registry.

### Add Config Keys

Add a list of environment variables that your services might need here. You are only adding the Config keys here. The values are entered when you are configuring an instance of your application.

### Add Environments

Click on the ![add icon](images/add_icon.gif) and enter a name for your environment. You can add any number of environments in this section.

## Formation Service

This is where you create your Service. You can combine the images and config keys created in your **Components** section to create multiple Services.

- Add images to your template
- Add Config Variables to your template
- Select the environment you want this template deployed to
- Once you are ready to deploy the Service, click on the icon next to the environment to configure an instance of your service.

## Formation Teams

This is where you can add team members who can view and manage your formation. You can add them as either **owners** or **collaborators**

