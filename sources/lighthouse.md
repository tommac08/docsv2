page_title: Shippable Lighthouse Image Watcher | Shippable
page_description: How to watch images using Shippable's Lighthouse Feature
page_keywords: lighthouse, shippable ci, documentation, shippable, watch docker images

# Lighthouse: Image Watcher

## Overview

Have you ever wasted your time debugging a broken build only to realize a few frustrated hours later that it was due to something that changed in one of the images you depend on?

This is where lighthouse can come to your rescue.

Lighthouse is available to all users of Shippable (yes, for free). Using this feature, you can add any image from any registry that you want to track and get notified via email when there is an update. It is a simple set up and we will be adding more functionality to this feature over time - so keep a watch on our watcher.

Today, we support watching images that are hosted on either Docker Hub or Google Container Registry

* * * * *

## Permissions

**UPDATE PERMISSIONS**

## Adding an image to the Lighthouse

### Step 1: Prequisite: Set up Account Integration for the registry

**If you have already set this up for your registry in Shippable, go to Step 2**

**Docker Hub**

- Login to Shippable
- Click on `Settings` in the top navbar
- On the Account Settings page, click on `Integrations`
- From the options presented, click on `Docker Hub Credentials`
- Enter an Integration name, which will be used to refer to this integration on Shippable
- Enter your credentials
- Click on `Save`

**GCR**

- Login to Shippable
- Click on `Settings` in the top navbar
- On the Account Settings page, click on `Integrations`
- From the options presented, click on `Docker Hub Credentials`
- Enter an Integration name, which will be used to refer to this integration on Shippable
- Enter your credentials
- Click on `Save`

### Step 2: Prerequisite: Add an email to receive notifications

**If you have already added email integration for lighthouse image watcher, go to Step 3**

- Login to Shippable
- Go to `Account Settings` on the top bar
- Click on the Integrations tab on the right
- Add a new Integration by adding a (+)
- Integration Name: `Example: my-shippable-emailid` (Tip:Use a description integration name you can remember)
- Master Integration: On the dropdown, choose `Email Notification for imagewatchers`
- Integration Type: Automatically set
- Email Address: `your_name@email.com`
- Click `Save`

### Step 3: Add an image to Lighthouse

 **[SCREENSHOT NEEDED]**

 - Go back to the Shippable Landing Page by clicking on the main Shippable Icon on the top left
 - Click on the dropdown and select Lighthouse at the very bottom
 - On the Lighthouse page, fill in the details as below:
 - Image Name: `your_repo_name/image_name` (the image from either DockerHub or GCR)
 - Integration: The Integration Name from Step 1 that you created to connect to the registry
 - Click on the `Save` Icon

### Step 4: Configure Notifications for the Lighthouse Image

 - Click on the image_name
 - This will open up the image modal where you can turn on notifications.
 - Scroll to the bottom where the email notifications that were set up in Step 2 will show up. By default, notifications are set to Off.
 - Use the toggle buttom to turn notifications ON for any email.

## Tags

 Lighthouse periodically polls the registry and the tags are updated when there is any update to the image in the source registry. You will see the latest tag against the image when the image is synced.

## Updating an Image on Lighthouse

To update an image or change the notification settings, go to the Shippable Landing Page by clicking on the Shippable Icon on the top right.

- Navigate to the bottom of the dropdowm and click on `Lighthouse`
- All images you have added will show up here.
- Click on the image_name to update the notification setting

## Deleting an Image on Lighthouse

 To delete an image on lighthouse, go to the Shippable Landing Page by clicking on the Shippable Icon on the top right.

- Navigate to the bottom of the dropdowm and click on `Lighthouse`
- You will see the list of images being watched on the Lighthouse Dashboard
- Click on the DELETE icon at the top of the table
- Now you can choose the images you want to delete by clicking on the delete icon against each of the images and clicking confirm.
