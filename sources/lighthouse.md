page_title: Shippable Lighthouse Image Watcher | Shippable
page_description: How to watch images using Shippable's Lighthouse Feature
page_keywords: lighthouse, shippable ci, documentation, shippable, watch docker images

# Lighthouse: Shippable's Image Watcher

## Overview

Have you ever wasted your time debugging a broken build only to realize a few frustrated hours later that it was due to something that changed in one of the images you depend on?

Well, Lighthouse to the rescue!

Lighthouse is available to all users of Shippable (yes, for free). Using this feature, you can add any image from any registry that you want to track and get notified via email when there is an update. It is a simple set up and we will be adding more functionality to this feature over time - so keep a watch on our watcher.

Today, we support watching images that are hosted on either Docker Hub or Google Container Registry

* * * * *

## Permissions

ADD DETAILS about who is allowed to add, modify or delete an image

## Adding an image to the Lighthouse

### Step 1: Prequisite: Set up Account Integration for the registry

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

If you have already set this up for Lighthouse once before, you can skip this and go to Step 2.

- Login to Shippable
- Go to `Account Settings` on the top bar
- Click on the Integrations tab on the right
- Add a new Integration by adding a (+)
- Integration Name: `Example: lighthouse-account name`
- Master Integration: On the dropdown, choose `Email Notification for imagewatchers`
- Integration Type: Automatically set
- Email Address: `your_name@email.com`
- Click `Save`

### Step 3: Add an image to Lighthouse

 **[SCREENSHOT NEEDED]**

 - Go back to your subscriptions page by click on the Lighthouse at the very bottom on the Dropdown
 - On the Lighthouse page, fill in the details as below:
 - Image Name: `your_repo_name/image_name`
 - Integration: The Integration from Step 1 above that we use to connect to the registry
 - Click on the `Save` Icon

 ## Tags




 ## Updating an Image on Lighthouse





 ## Deleting an Image on Lighthouse



