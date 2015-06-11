page_title: Shippable Account Settings | Shippable
page_description: Account Settings, Integratons, Account Tokens, Credit Cards
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation, Tokens, account settings

# Account Settings

You can get to your Account Settings by clicking on the _gear_ icon on the top bar to the right.

This page will explain the various options in Account Settings Page

## Account Information

**Id:** This is your account id in Shippable. This is the id that is linked to any access tokens you create or credit cards you add to your account.

**Force Sync:** We periodically sync account info with Github and Bitbucket. Currently this is every hour. Use this button to sync your account if you would like to see any changes made upstream immediately. Example: You have been added to a new subscription as an owner/collaborator; you have new repos in github and want to see it immediately; you linked your bitbucket account to this account and want to see your bitbucket repositories right away.

## Github Identity

If you are using a Github Account to log in, this displays the github user settings.

**Public On:** This indicates that we have access to your Public Repos in Github. This is turned on by default, since signing into Shippable requires you to authorize this access on Github.

**Private On/Off:** This indicates whether Shippable has access to your Private Repos. This needs to be turned on for Shippable to run any builds on repos that are private on Github.

> **Note**
>
> It is not possible to turn OFF the Private repos, once you have given us access. That would
> require you to delete the Shippable Account and go through the sign up process again.

## Bitbucket Identity

If you are using a Bitbucket Account to log in, this displays the bitbucket user settings.

**All Repos ON:** Bitbucket does not separate public and private repo access, so this is turned on by default when you sign in and authorize Shippable to access Bitbucket.

_If you have accounts on both Github and Bitbucket, you can link both of these here, so you see a combined list of repositories on your Shippable Account_

## Misc Settings

**Show Private Forks:** If you would like to track forks from your repo (you must be an owner of the repo), then set this to ON. This is OFF by default.

## Delete Account

Clicking on this will delete all account data, project data, build data and formation data from our systems.

