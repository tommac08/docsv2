page_title: go | Documentation | Shippable
page_description: How to configure shippable.yml file for go
page_keywords: yml,go,test,tip

# Go

This section helps you to create a shippable.yml file for your GO
project:

- Set the appropriate language and the version number. You can test against multiple versions for a single push by adding more entries. GO minions use `go` by default to set the runtime platform:

         # language setting
         language: go
         # version numbers
         go:
           - 1.1
           - 1.2
           - 1.3
           - release
           - tip

- Install dependencies for your project using the **install** key:

         install:
           - go get github.com/onsi/gomega
           - go get github.com/onsi/ginkgo

- **Test scripts** : Use the **script** key in the shippable.yml file to specify what command to run tests with:

         # command to run tests
         script: go test -v ./...

## Build Examples

This sample will help you get started with Shippable. The testing
framework used here is [Ginkgo](http://onsi.github.io/ginkgo/).

[Go Sample](https://github.com/shippableSamples/sample_go)

[Go Sample with MongoDB](https://github.com/shippableSamples/sample_go_mongo)

[Go Sample with PostgreSQL](https://github.com/shippableSamples/sample_go_postgres)

Copy the test and code coverage output into the special folders
Shippable/testresults and Shippable/codecoverage to get the reports
parsed. The test report must be in JUnit format.

We need the yml file to analyze your project details. So add the
shippable.yml file to the root of your repo by specifying:

**language :** Go is used in this sample project

**version number :** 1.2 is the version used in this sample project.

Here is the complete yml file for sample_go

```yaml
language: go

go:
  - 1.2

install:
  - go get code.google.com/p/go.tools/cmd/cover

# Make folders for the reports
before_script:
  - mkdir -p shippable/testresults
  - mkdir -p shippable/codecoverage

script:
  - export GOPATH=$PWD
  - export PATH=$PATH:$GOPATH/bin
  - go get github.com/t-yuki/gocover-cobertura
  - go get github.com/onsi/gomega
  - go get github.com/onsi/ginkgo
  - go install github.com/OneRedOak/sample
  - go test -coverprofile=coverage.txt -covermode count github.com/OneRedOak/sample
  - gocover-cobertura < coverage.txt > coverage.xml

after_script:
  - mv $PWD/src/github.com/OneRedOak/sample/junit.xml $PWD/shippable/testresults/
  - mv $PWD/coverage.xml $PWD/shippable/codecoverage/
```

Enable the repo sample_go and run it using an Ubuntu minion. Once the
build finishes execution, you can check for the console output, test and
codecoverage results on the respective build's page.

