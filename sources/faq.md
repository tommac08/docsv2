page_title: FAQ
page_description: Commonly asked questions that will help with troubleshooting
page_keywords: concepts, documentation, shippable, CI/CD

# FAQ

Having trouble with your builds? Here is a list of frequently asked
questions.... hope this helps!

## How do I update my Shippable plan?

Go to `Settings` under either your CI Subscription Page or Formations Page. You will update your plan from either of these places.

Documentation:[Subscription Plan Management](ci_settings/#subscription-plan-management)

## How are permissions managed on Shippable?

We closely mimic GitHub and Bitbucket permissions for Orgs and projects.
Anyone who has access to an organization or repository in
GitHub/Bitbucket will also have access to build information and/or
repository and build actions on Shippable. This happens automatically,
so if you enable a repository in your Org on Shippable and another team
member signs in, they will see the enabled repository and build history
already present in their account.

We support 2 roles -

**Owner :** Owners have all privileges for an Org or Project. They can
enable, run and delete projects, upgrade pricing plans, and view/run,
cancel, and delete builds.

**Collaborator :** Collaborators can enable projects and view/run builds
on Shippable. They cannot delete enabled projects or upgrade pricing
plans.

## Why can't I see some of my repositories in my Shippable account?

This happens due to one of the following reasons:

- You haven't enabled private repositories in your Shippable account. Go to our [Account Settings Documentation](account_settings.md) to learn how to turn on Private Repos for your Github Account.
- Your account hasn't yet been synced with the latest permissions from GitHub. To force sync your account, click on the `Force Sync` icon in your Account Settings. (the `gear` on the top nav bar)
-  You're a BitBucket user and you have mercurial repositories. We do not support mercurial at this time, so you will need to convert them to git or use another platform for CI.

## Why do I get an error when I try to enable a project that is listed on my dashboard?

This usually happens if you are a collaborator on a project and the
owner of the project has not given Shippable access to the project. You
can verify this by confirming that the owner of the project can see the
project on their Shippable dashboard.

## I have enabled my repository and committed code, but my build doesn't start. What could be wrong?

Please check the 'Notifications' tab on your repository page on
Shippable. If it shows any errors, fix those and try again.

If the error shows a parsing failure for the yml, you can validate the
file at [YAML Lint](http://www.yamllint.com/).

## Why can't I see my BitBucket repos in my Shippable account?

Shippable only supports git based repositories, so if you have mercurial
repositories in your BitBucket account, you will not see them in the
Shippable repository list. If you cannot see git based repos, please
open an issue on our [GitHub Support
repo](<https://github.com/Shippable/support>).

## Why can't shippable see my org on github?

Github's default policy when a new org is created is 'access
restricted'. In order for Shippable to be able to see the org, you must
manually grant access to Shippable. This can be resolved by going to the
third-party access section for the org, and clicking 'Remove
restrictions' Under the 'Third-party application access policy' section.

## How do I link my github and bitbucket accounts?

Check out our documentation on [linking bitbucket and github accounts](account_settings/#linking-your-bitbucket-and-github-accounts)

## Why am I not able to see BitBucket org repos after deleting and recreating my account on Shippable?

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
