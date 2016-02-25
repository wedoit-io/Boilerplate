Boilerplate
===========

This repository contains (along with the README you're reading right now) contains some of the best practices to work as an ASP.NET developer as well as common tasks when starting up with Azure, etc.


## Initial Requirements

Following is the recommended setup to work as a developer.

:warning: **Be aware that if you don't do these first, the rest of this guide will be _very_ hard to accomplish.**

0. Install [GitHub Desktop](https://desktop.github.com/) and set your default shell to "Git Bash" in options.

  > :information_source: You could install [Mintty](https://mintty.github.io/) (or any other terminal emulator) and [Git](https://git-scm.com/downloads) separately instead, but _we think_ the solution above is much easier. Although you may prefer **not** use GitHub Desktop, "Git Shell" (which is installed along with it) is *super* handy.

0. Done. :wink:


## Default Azure Setup

Following is the recommended setup to work with Azure.

0. Install [node.js (stable)](https://nodejs.org/en/#download).

0. Install [Azure CLI](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/) and then make sure `azure --version` prints out something similar to `0.9.15 (node: 4.2.4)`.

  > :information_source: You may need to add `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin` to your `PATH` to make this work.


## How to Create a New Project

Following the instructions you should be able to create a new VS solution and a git repository using this boilerplate.

0. Install [NuGet command line utility](https://docs.nuget.org/consume/command-line-reference#user-content-installing)

0. In your terminal...
  ```bash
  ## First off `cd` into wherever you want to create project folder
  cd <somewhere>

  ## Set some variables...
  PROJECT_NAME='foobar'
  GH_USER='user'
  GH_REPO='repo'

  ## 1. Create project directory
  ## 2. Download & extract the boilerplate
  ## 3. Setup files and contents using project name
  ## 4. Create empty git repository
  ## 5. Add files to source control
  ## 6. Add remote and push to GitHub
  mkdir $PROJECT_NAME
  curl -sL https://github.com/Apex-net/TryAzure/archive/master.tar.gz | tar -xzC $PROJECT_NAME --strip-components=1
  cd $PROJECT_NAME
  mv Boilerplate.sln "$PROJECT_NAME.sln"
  git init && git commit --allow-empty -m"First commit."
  git add . && git commit -m"Initial project structure."
  git remote add origin "https://github.com/$GH_USER/$GH_REPO.git"
  git push --set-upstream origin master
  ```

### Additional Steps & Associated Services

* Enable [branch protection](https://help.github.com/articles/configuring-protected-branches/) for `master` in GitHub with [required status checks](https://help.github.com/articles/enabling-required-status-checks/)
* Enable [AppVeyor](https://www.appveyor.com) Continuous Integration
* Enable [Continuous deployment with GitHub and Azure](https://github.com/blog/2056-automating-code-deployment-with-github-and-azure)


## How to Deploy on Azure

Following steps below you will:

* Login to Azure using **Microsoft recommended** [Resource Manager mode (`arm`)](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-command-line-tools/) even though [Service Management mode (`asm`)](https://azure.microsoft.com/en-us/documentation/articles/azure-cli-arm-commands/) (a.k.a. "Classic mode") is currently enabled by default when you first install the Azure CLI.
* Create a new resource group that contains;
  * a "App Service plan" (a.k.a. "server farm")
  * a "Web app" inside the server farm
  * a "SQL server"
  * a "SQL database"
* Connect the SQL database to the web app.

```bash
azure config mode arm
azure login # ... follow instructions on the screen to login

## Decide how you want to call things...
RES_GROUP_NAME='example-resgroup'
WEBAPP_NAME='example-webapp'
FARM_NAME='example-farm'
## You can leave location name empty, it defaults to `westeurope`,
## or use `azure location list` to select a different location
LOCATION_NAME=''

## 1. Create resource group
## 2. Create server farm (ref.: http://stackoverflow.com/questions/35511709/create-a-server-farm-aka-app-service-plan-from-the-command-line/)
## 3. Create web app
azure group create --verbose $RES_GROUP_NAME ${LOCATION_NAME:-westeurope}
azure resource create --verbose $RES_GROUP_NAME $FARM_NAME "Microsoft.Web/ServerFarms" ${LOCATION_NAME:-westeurope} "2015-06-01" --properties "{\"sku\":{\"tier\": \"Free\"},\"numberOfWorkers\":1,\"workerSize\": \"Small\"}"
azure webapp create --verbose $RES_GROUP_NAME $WEBAPP_NAME ${LOCATION_NAME:-westeurope} $FARM_NAME
```

### How to Destroy Everything You Just Created

:warning: **CAUTION: THERE'S NOT GOING BACK!**

```bash
azure group delete --verbose $RES_GROUP_NAME
```


## Setup

```bash
script/setup
```
