page_title: Shippable CI Build Flow | Documentation | Shippable
page_description: This section describes how to set up tests and run a build on Shippable
page_keywords: getting started, questions, documentation, shippable, config, yml

# Build Flow for CI

These are the steps that are executed when we receive a build trigger automatically via a webhook or manually through the UI.

## Build Steps

1.  Clone/Pull the project from GitHub or Bitbucket. This depends on
    whether the minion is in pristine state or not
2.  `cd` into the workspace
3.  Checkout the commit that is being built
4.  Run the `before_install` commands. This is typically used to prep
    your minion and install/update any packages
5.  Run `install` section. This should be used to install any project
    specific libraries or packages
6.  Run `before_script` commands. Create any folders and unzip files
    that might be needed for testing. Some users also restore DBs, copy
    environment variables, etc. here
7.  Run the `script` commands. This runs the build and all your tests
8.  Run either `after_success` or `after_failure` commands, depending on
    the result of your build. after\_success can be used to deploy to
    any supported cloud provider
9.  Run `after_script` command

Build status is determined based on the outcome of the above steps. They
need to return an exit code of `0` to be marked as success. Everything
else is treated as a failure.

Any errors in `after_script` will not affect the status of the build.

-------

Read our [Build guide](build_case2) to learn how to run a build.

## Build termination

Build will be forcefully terminated in the following scenarios:

-   If there has not been any log output or a command hangs for 10 minutes
-   If the build is still running after 60 minutes for Free Plans or 120 minutes for Paid Plans

When a build is forcefully terminated, the build status will indicate **timeout**.

