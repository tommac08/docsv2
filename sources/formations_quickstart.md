page_title: Setting up your first formation | Shippable
page_description: Quick start formations setup documentation
page_keywords: getting started, formations, quick start, documentation, shippable

# Setting up your First Formation

## **Step 1** : Sign up for a Formation Plan

* Add section for Existing User
* Add section for New User

---

## **Step 2** : Make sure your account integrations are up-to-date

* To make sure formations can pull images from the registry of your choice, make sure your integration is set up correctly for either Docker Hub or Google Container Registry or both
* Click on the Account Settings icon on the top bar
* Click on the Integrations Tab
* Click on the (+) to add a new integration
* Add a Name - Use a name that lets you remember what it is when you are using it later. Example: myaccount-dockerhub
* Choose `Docker` as the Master Integration

## **Step 3** : Go to Formations Page

- Login to Shippable at https://wwww.shippable.com
- You can login using either your Github or Bitbucket account
- On the "Select" dropdown, Choose "Add a Formation"
- This will bring you to the Formations UI

## **Step 3** : Add the required components to your Formation

Each Formation is expected to have the following components:
1. Images
2. Configuration Values
3. Environments

- On the right sidebar, click on 'Components'
- First add images to your formation (this requires you to have Integrations set up to pull images from the registry of your choice. See Step 1 above)

