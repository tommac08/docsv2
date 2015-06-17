page_title: Shippable Build Dashboard | Documentation | Shippable
page_description: Walk through the Build Dashboard for Shippable CI
page_keywords: build page, build dashboard, CI/CD, shippable CI, documentation, shippable, config, yml

# CI Build Dashboard

This page walks through the build dashboard for your CI project where you can view the details, streaming logs, coverage reports and so on.

## How do you get here?

You can get to the builds dashboard in a few different ways:

- By clicking on a specific build from your [project dashboard](project_dashboard)
- By clicking on the build from the Current or Running builds on the Shippable landing page
- By clicking on a build link in the notification emails you receive

## Build Matrix

If you have a [matrix build](yml_reference/#build-matrix), each build opens up as a different tab that will have the following sections.

## Build Info

The Info section has details about a build such as:

- Build Status
- Who triggered the build (whether it was manual or via webhook)
- When the build was triggered
- Build Duration
- Commit Message
- Matrix Values
- Environment Vars

## Build Console

This is where you can see the different build steps being executed. These are streamed real-time to the browser via websockets

## Tests

If you have tests configured in Shippable/testresults, this is where you can see it displayed.

## Coverage

If you have coverage reports configured in Shippable/codecoverage, they are parsed and displayed in this section.

For more details on adding Tests and Coverage to your builds, read our [Build Guide](build_case2)

## Script

This is the build script that is being run. This will show anyone interested the environment variables being set and the commands being run while executing your build.


