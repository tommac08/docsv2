page_title: Permissions and Access on Shippable | Documentation | Shippable
page_description: explains how permissions work on shippable
page_keywords: how-to, permissions, access questions, documentation, shippable, Continuous Integration, Continuous Deployment, CI/CD

# Access and Permissions

This page explains the levels of access Shippable has to your repos and how permissions work.

## OAuth Authentication

We use OAuth authentication. What this means is if you have either a Github or Bitbucket account, you do not need to create a separate account on our platform.

## Access to repos

When signing up to Shippable, you will be prompted to give Shippable access to your repos. GitHub and Bitbucker auth behave a little differently as follows -

**GitHub**- By default, we will only ask for access to public repos. If you want to use Shippable to build your private repos, you will need to authorize us for private repositories. This is done from your [Account Settings Page](account_settings.md).

**Bitbucket**- The Bitbucket API does not have public/private
granularity, so we ask for access to all repos on Bitbucket by default.

> **Note**
>
> We realize that most people do not want to give write access to their
> repo. However, we need write permissions to add deploy keys to your
> repos for our webhooks to work. We do not touch anything else in the
> repo.

## Permissions

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

