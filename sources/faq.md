page_title: FAQ | Documentation | Shippable
page_description: Commonly asked questions that will help with troubleshooting
page_keywords: concepts, documentation, shippable, CI/CD

# FAQ

Having trouble with your builds? Here is a list of frequently asked
questions.... hope this helps!

### How can I update my Shippable plan?

Shippable CI/CD and Shippable Formations are 2 different subscriptions under your account. These need to be purchased separately.

First make sure you have a valid payment method on Shippable.

- Click on the Account Settings icon on the top nav bar
- Click on the **Cards** tab
- Add a new payment method by clicking on the ![add icon](images/add_icon.gif)

To purchase additional containers for Shippable CI:

- Click on **CI** on the Shippable Landing page
- Click on your **CI Subscription** in the dropdown
- Go to the **Billing** tab
- Make sure your plan is set to `Multi-Tenant CI`
- Use the slider to select the number of containers you want for your subscription.
- Enter your payment details and click `Buy`
- Your CI Plan is now updated

To purchase containers for a new or existing Shippable Formation:

- Click on **Formations** on the Shippable Landing page
- Click on **Add a new formation** in the dropdown
- This will take you to our payment page. Choose `Multi-Tenant Formation` as your plan
- Use the slider to select the number of containers you want for your formation
- Enter your payment details and click `Buy`
- You are ready to start on your new formation

To update containers in an existing Shippable Formation:

- Click on **Formations** on the Shippable Landing page
- Choose the Formation that you want to update
- Go to the **Billing** tab
- In the first section, choose `Multi-Tenant Formation` as your plan
- Use the slider to select the number of containers you want for your formation
- Enter your payment details and click `Buy`
- Your plan is now updated


### Why can't I see some of my repositories in my Shippable account?

This happens due to one of the following reasons:

- You haven't enabled private repositories in your Shippable account. Go to [Account Settings](account_settings.md) and in the **GitHub Identity** section, click on the **Private Repos OFF** icon. This is a one-way toggle button to turn on Private Repos for your GitHub account.
- Your account hasn't yet been synced with the latest permissions from GitHub. To force sync your account, go to your Account Settings and click on the `Force Sync` icon next to your Account Id.
-  You're a Bitbucket user and you have mercurial repositories. We do not support mercurial at this time, so you will need to convert them to git or use another platform for CI/CD.

## Why do I get an error when I try to enable a project that is listed on my dashboard?

This usually happens if you are a collaborator on a project and the
owner of the project has not given Shippable access to the project. You
can verify this by confirming that the owner of the project can see the
project on their Shippable dashboard.

## I have enabled my repository and committed code, but my build doesn't start. What could be wrong?

Please check the 'Notifications' tab on your repository page on Shippable. If it shows any errors, fix those and try again.

If the error shows a parsing failure for the yml, you can validate the
file at [YAML Lint](http://www.yamllint.com/).

## Why can't I see my BitBucket repos in my Shippable account?

Shippable only supports git based repositories, so if you have mercurial
repositories in your BitBucket account, you will not see them in the
Shippable repository list. If you cannot see git based repos, please
open an issue on our [GitHub Support
repo](<https://github.com/Shippable/support>).

## Why can't Shippable see my org on GitHub?

GitHub's default policy when a new org is created is 'access
restricted'. In order for Shippable to be able to see the org, you must
manually grant access to Shippable. This can be resolved by going to the
third-party access section for the org, and clicking 'Remove
restrictions' Under the 'Third-party application access policy' section.

## How do I link my GitHub and Bitbucket accounts?

Please read our documentation on [linking GitHub and Bitbucket accounts](link_gh_and_bb.md).

## Why am I not able to see Bitbucket org repos after deleting and recreating my account on Shippable?

Deleting the shippable account will also delete the permissions
associated with the account. If you recreate your account, bitbucket
will not allow us to pull all the permissions you have, unless the owner
of that organization logs in back to shippable and then click on the
sync repos button to see the repos.

## How do I set desired timezones inside the minions?

By default, our minions are configured with ETC/UTC timezone which is
set in /etc/timezone file for ubuntu minions. However, we allow you to
set a specific time zone for the minion in before\_script section of
your yml file . For example,

```yml
before_script:
  - echo 'Europe/Paris' | sudo tee /etc/timezone
  - sudo dpkg-reconfigure --frontend noninteractive tzdata
```

This will change your minion timezone to paris time. Refer the article
[list of tz database time zones](http://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to select the timezone for your location.

## How do I skip webhook builds?

Any changes to your source code will trigger a build automatically on
Shippable. If you do not want to run build for a particular commit,
then add **[ci skip]** or **[skip ci]** to your commit message.

Our webhook processor will look for the string **[ci skip]** or **[skip
ci]** in the commit message and if it exists, then that particular
webhook build will not be executed.
