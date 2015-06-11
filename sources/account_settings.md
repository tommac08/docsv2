page_title: Shippable Account Settings | Documentation | Shippable
page_description: Account Settings, Integratons, Account Tokens, Credit Cards
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation, Tokens, account settings

# Account Settings

You can get to your Account Settings by clicking on the _gear_ icon on the top bar to the right. This page will explain the different options available in the Account Settings Page.

## Account Info

### Account Information

**Id:** This is your account id in Shippable. This is the id that is linked to any access tokens you create or credit cards you add to your account.

**Force Sync:** We periodically sync account info with Github and Bitbucket. Currently this is every hour. Use this button to sync your account if you would like to see any changes made upstream immediately. Example: You have been added to a new subscription as an owner/collaborator; you have new repos in github and want to see it immediately; you linked your bitbucket account to this account and want to see your bitbucket repositories right away.

### Github Identity

If you are using a Github Account to log in, this displays the github user settings.

**Public On:** This indicates that we have access to your Public Repos in Github. This is turned on by default, since signing into Shippable requires you to authorize this access on Github.

**Private On/Off:** This indicates whether Shippable has access to your Private Repos. This needs to be turned on for Shippable to run any builds on repos that are private on Github.

> **Note**
>
> It is not possible to turn OFF the Private repos, once you have given us access. That would
> require you to delete the Shippable Account and go through the sign up process again.

### Bitbucket Identity

If you are using a Bitbucket Account to log in, this displays the bitbucket user settings.

**All Repos ON:** Bitbucket does not separate public and private repo access, so this is turned on by default when you sign in and authorize Shippable to access Bitbucket.

_If you have accounts on both Github and Bitbucket, you can link both of these here, so you see a combined list of repositories on your Shippable Account_

### Misc Settings

**Show Private Forks:** If you would like to track forks from your repo (you must be an owner of the repo), then set this to ON. This is OFF by default.

### Delete Account

Clicking on this will delete all account data, project data, build data and formation data from our systems.

*****

## Integrations

This is where we collect the information needed to connect to the different services or apps that are required as part of your build or deployment workflow within Shippable. This is done once for each type of integration and can be re-used across different parts in the Shippable pipeline.

### Connect to Docker Hub

You will need this to pull or push images to Docker Hub as part of building your project. More details on using Docker Hub is [here](dockerhub.md)

1. To add a new integration, click on (+)
2. **Master Integration:** Click on `Docker`
3. Enter a name for your integration. Use a distinctive name that's easy to associate to the integration and recall. `Example:docker-mydockerusername`
4. Enter your credentials
5. Click on `Save`

### Connect to GCR (Google Container Registry)

You will need this to pull or push images to Google Container Registry as part of building your project. More details on using GCR is [here](gcr.md)

1. On the [Google Developers Console](https://console.developers.google.com/), select the project you just created
2. In the sidebar on the left, expand 'APIs & auth' and select 'Credentials'
3. Click 'Create new Client ID' and select 'Service Account' in the pop-up window
4. Click on 'Create Client ID'. A dialog box appears. To proceed, click 'Okay, got it'
5. Your new Public/Private key pair is generated and downloaded to your machine. Please store this carefully since you will not be able to retrieve this from your GDC account.
6. Now you are ready to integrate with GCR on Shippable. Back to [Shippable](https://shippable.com).
7. Click on Settings and then the Integrations Tab.
8. To add a new integration, click on (+)
9. **Master Integration:** Click on `GCR`
10. Enter a name for your integration. Use a distinctive name that's easy to associate to the integration and recall. `Example:gcr-gcrusername`
11. Enter your JSON key that you saved earlier
12. Click on `Save`

### Set up Notifications for Images on Lighthouse

This is the integration that is used to set up email notifications for Lighthouse. To learn more about lighthouse, go [here](lighthouse.md)

1. To add a new integration, click on (+)
2. **Master Integration:** Click on `Email Notification`
3. Enter a name for your integration. Use a distinctive name that's easy to associate to the integration and recall. Example: `Example: my-shippable-emailid`
4. Enter your email address: `janedoe@shippable.com`
5. Click on `Save`

*****

## Tokens

This is where you generate, view and manage access tokens to use our [API](api.md).

- Enter a name for your token
- Click `Generate Token`
- Remember to copy the token. For security reasons, the token will never be displayed again.
- To delete a token, click on the _delete_ icon next to your token name

> **Note**
>
> NEVER commit code containing your API token to a public repository.
> Doing so will compromise the security of your Shippable account. Treat
> your API token like a password

*****

## Cards

This allows you to manage your payment instruments on Shippable. It will list the credit cards that are currently linked or used previously. You can add a new card or remove cards from this tab.
