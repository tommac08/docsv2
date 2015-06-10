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
-

To enable a repository for CI -

-	Click on 'Repositories' on the right sidebar of your dashboard.
-	Find or search for your repository in the list, and click the **Enable** button.

Now, whenever you push a commit to your GitHub/Bitbucket repository, Shippable will build that project as long as you have a
shippable.yml (or .travis.yml) at the root of your repository.

---

## **Step 3** : Create YML file

We require a shippable.yml file at the root of the repository you want
to build with Shippable. This yml file is the config for your build.

> **Note**
>
> If you use TravisCI, we support `.travis.yml` natively, so that
> you can test your repos in parallel with Shippable and compare the
> speed and rich visualizations.

Your yml needs a couple of entries at the very minumum - the language
and the version(s) of the language you want to test against.

```python
# language setting
language: node_js

# version numbers, testing against two versions of node
node_js:
    - 0.10.25
    - 0.11
```

The rest of the yml is composed of standard sections - `before_install`,
`install`, `before_script`, `script`, `after_success` or
`after_failure`, and `after_script`.

If you just have the language and language version in your yml, we will
attempt to 'guess' at the other settings and build your project.
However, in most cases, you will need to specify additional
configuration in the yml.

**Complete documentation of YML is available** [HERE](yml_overview/).

---

## **Step 4** : Setup Test Visualizations

To use Shippable's test visualization feature, your code coverage output
needs to be in cobertura xml format, and test results should be in junit
format. More details can be found in our [Code Samples](languages/).
This is an optional feature.

---

## **Step 5** : Run the build

Builds can be triggered through webhooks or manually through
shippable.com.

**Webhooks**

Our webhooks are triggered when a commit is pushed to your repo, or if a
pull request is created. Webhooks are a code way to verify that commits
to your project build in a clean environment, and not just on the
committer's machine.

**Manual Builds**

After enabling the project, click the **Build this project** button to
manually run a build. Instantly, it will redirect you to the build's
page and the console log from your build minion starts to stream to your
browser through sockets.

---

## **Step 6** : Check output

In addition to running builds, Shippable also provides useful
visualizations for every build.

**Console Log**: Stdout of a build run is streamed to the browser in
real-time using websockets. In addition, there are other important
pieces of information like

-   build status
-   duration
-   GitHub changeset id
-   committer info

**Artifact archive**: If enabled, build artifacts are automatically
archived for each run upon completion. To download a tarball of your
build's artifacts, go to the build's page and click the **Artifacts**
button. All files in the ./shippable folder at the root of the project
are automatically archived. Make sure you include the **archive: true**
tag in your yml file to enable the download archive button.

**Test cases**: Test run output is streamed in real-time to the console
log when the tests are executed. If you want Shippable's parser to parse
test output and provide a graphical representation, you need to export a
JUNIT xml of your test output to the ./shippable/testresults folder.
After the build completes, our build engine will automatically parse it
and the results will appear in the Tests tab (available in build's
page).

**Code Coverage**: Executing tests is only useful so far as the tests
cover your code. A variety of coverage tools like opencover, cobertura
etc. provide a way to measure coverage of your tests. You can export the
output of these tools to `./shippable/codecoverage` and our build engine
will automatically parse it. The results will appear on the Coverage
tab.

Clicking the **View build history** button will take you to the
project's page where you can find a complete history of your project's
builds.

