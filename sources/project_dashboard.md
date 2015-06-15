page_title: Shippable Project Dashboard| Documentation | Shippable
page_description: Explanation of the Shippable Project Dashboard
page_keywords: project dashboard, CI/CD, shippable CI, documentation, shippable, config, yml

# CI Project Dashboard

This page walks through the details on the Project Dashboard. You get here by clicking on a specific project from your [CI Dashboard](ci_dashboard)


## Build badge

Badges will display the status of your default branch. You can find the build badges on the project's page. Click on the **Badge** button and copy the markdown to your README file to display the status of most recent build on your Github or Bitbucket repo page.

-----

## Build termination

Build will be forcefully terminated in the following scenarios:

-   If there has not been any log output or a command hangs for 10 minutes
-   If the build is still running after 60 minutes for Free Plans or 120 minutes for Paid Plans

When a build is forcefully terminated, the build status will indicate **timeout**.



