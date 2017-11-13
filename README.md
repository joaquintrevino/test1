# Shape Connect

## Contents
- [Documentation](#documentation)
- [Prerequisites](#prerequisites)
- [Setting up a local instance of the project](#setting_up_a_local_instance_of_the_project)
    -  [1. Setting up the config file](#1_setting_up_the_config_file)
    -  [2. Setting up tests](#2_setting_up_tests)
    -  [3. Setting up the correct Node version](#3_setting_up_the_correct_node_version)
    -  [4. Install modules](#4_install_modules)
    -  [5. Setting up Google Cloud Datastore emulator](#5_setting_up_google_cloud_datastore_emulator)
    -  [6. Webpack](#6_webpack)
    -  [7. Running the project](#7_running_the_project)
- [Installing git hooks](#installing_git_hooks)
- [Deploying](#deploying)
- [API docs](#api_docs)
- [Running the tests](#running_the_tests)
- [Unit tests](#unit_tests)
- [Coverage tests](#coverage_tests)
- [Functional tests](#functional_tests)
- [Reports](#reports)
- [Authentication](#authentication)
- [Using Google’s IAP](#using_googles_iap)
- [Simulated Athentication](#simulated_authentication)

## Documentation
- [Description](doc/description.md)
- [Test plan](doc/test_plan.md)
- [Contributing Guidelines](.github/CONTRIBUTING.md)

## Prerequisites
| Prerequisite                                | Version |
| ------------------------------------------- | ------- |
| [Node.js](http://nodejs.org)                | `~ ^8.9`  |
| npm (comes with Node)                       | `~ ^5.5.1`  |
| [nvm](https://github.com/creationix/nvm#install-script)                                                                          | `~ ^0.31` |
| [Java](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)                                      | `~ ^8`  |

## Setting up a local instance of the project

### 1. Setting up the config file
#### Backend
1. The `config/default.json` file provides base configuration
1. If you want to override or extend the default configuration, place a file containing your environment variables under the `config/`
folder. Use `example.json` as a base.
1. Name it depending on your environment (`production.json`, `development.json`
or `local.json`) which will be taken from `NODE_ENV`.
1. `development.json` is taken by default if `NODE_ENV` variable is not set.
#### Frontend
1. The configuration for the frontend is located in `config/frontend/development.json` and `config/frontend/production.json`.
1. `development.json` corresponds to `webpack.dev.config.js` and `production.json` to `webpack.config.js`.
1. Set the `API_HOST` to point to your ShapeConnect API Endpoint.
1. This will be loaded using the Webpack resolver so check the config files (either `webpack.config.js` or `webpack.dev.config.js` according to your environment).

### 2. Setting up tests
1. Make sure to have a `config/test.json` file for the tests to run.
1. If there's no need to override the default variables just leave an empty object (i.e. `{ }`)

### 3. Setting up the correct Node version
To run the project we first need to verify we have the right node version. For this we use a `.nvmrc` file containing the version number of Node that is going to be used.

1. Install [Node Version Manager](https://github.com/creationix/nvm).
1. Use `nvm use` to switch to the Node version the project's been built for.
1. If the Node version is not installed just hit `nvm install` and then `nvm use`.

If you prefer not to use `nvm` you can just simply install the Node version
specified in `.nvmrc` by hand.

### 4. Install modules
Inside the project's directory run `npm install` to install the project's dependencies.

### 5. Setting up Google Cloud Datastore emulator
We use the Google Cloud Datastore to manage our emulation data. This datastore holds the user's information that is used while running the application. We will connect to the Google Cloud datastore tool to grab a copy of the data to have it available locally. 
1. Install Google Cloud SDK if you don't have it yet (more info in Deployment section or download from [here](https://cloud.google.com/sdk/downloads) and follow the instructions).
1. Install the Datastore emulator by running `gcloud components install gcd-emulator`.
1. Export the following environment variables:
    * `export GCLOUD_PROJECT=shape-163518`
    * `export GOOGLE_APPLICATION_CREDENTIALS=shape-connect-dev-credentials.json`
    * `export DATASTORE_EMULATOR_HOST=localhost:8081`
    * `export DATASTORE_PROJECT_ID=shape-163518`
1. Ask the team for `shape-connect-dev-credentials.json` or get your own from google cloud console and paste it on the root of the project.
1. Run `gcloud beta emulators datastore start --data-dir data` to finish up configuration and install missing dependencies.

##### Useful datastore commands
These are commands that can be run to generate data. (Further explanation on what these can do can be found on the package.json file in the repository)
| Command                                | Action |
| ------------------------------------------- | ------- |
| `npm run start:datastore`               | Start emulator  |
| `npm run generator`               | Insert data to datastore. This command runs a generator file that grabs json data from `server/emulator/data directory`  |

##### Switching from emulator to Google Cloud datastore instance
As long as `DATASTORE_EMULATOR_HOST` and `DATASTORE_PROJECT_ID` variables are present in the environment, application is going to connect to emulator, if you want to connect to GCP Datastore instances `unset` both environment variables and make sure `GCLOUD_PROJECT` and `GOOGLE_APPLICATION_CREDENTIALS` are set.

`unset DATASTORE_EMULATOR_HOST`

`unset DATASTORE_PROJECT_ID`

To get more information about how Backend Emulator works run `npm run docs` then click `Backend Emulator` link in Related documents table.

### 6. Webpack
The project uses [Webpack](https://webpack.js.org/) to run. This tool was installed in the [4. Install modules](#4_install_modules) step. You can run the Webpack Dev Server if you set `USE_WEBPACK_DEV_SERVER` to `true` . If not set it defaults to `false`.


### 7. Running the project
Now that we have all the dependencies installed we can run the project using the following commands:

`npm run start:dev`

or

`npm run start:datastore`

`npm start`

Then, go to [http://localhost:8080/wizard/create](http://localhost:8080/wizard/create)

To stop the application run `npm stop`

## Installing git hooks
```
$ cd ./git/hooks/
$ ln -s ../../hooks/pre-push.sh pre-push
$ ln -s ../../hooks/pre-commit.sh pre-commit
```

## Deploying
Make sure you have `gcloud` installed. `gcloud` is a part of the Google Cloud SDK. You must [download and install the SDK](https://cloud.google.com/sdk/downloads) on your system and [initialize it](https://cloud.google.com/sdk/docs/initializing) before you can use `gcloud`.

Create a production config like this:

```
{
  "BACKEND_PORT": "8080",
  "USE_WEBPACK_DEV_SERVER": false
}
```

To deploy using `gcloud`, set `NODE_ENV` to `production`. On `app.yaml`:

```
env_variables:
  NODE_ENV: 'production'
```

Once you do that, run:

```
$ npm run deploy
```

This will build the project and then deploy it with `gcloud`.


## API Docs
We use `Swagger` to document the APIs. With the project running execute:
`npm run docs-api` .

It will open Swagger web editor, json spec is in your clipboard already, just paste it in the left panel.

## Running the tests

We use `npm test` to run all test (UI, API and Functional) but the application must be down.
As part of the first-time setup of the project you have to run `npm run install:selenium` to install `selenium-standalone` service.

## Unit tests
To run UI tests, the application must be down

`npm run test:ui`

**Note:** If you want to run Unit test and update snapshots run `npm run test:update`

## Coverage test
To run coverage test

`npm run test:coverage`

## Functional tests
Make sure you have Java 8 installed and running in your command line.

The first time you run the tests you need to run Selenium server `install` command.

`npm run install:selenium`

Then you can run functional tests, the application must be down

`npm run test:functional`

Another way to run functional tests, the application must be running

`npm run test:selenium`

It will start Selenium Standalone server, application server, run the test suite
and stop the servers. Consider that to stop the server it will kill process
running at port 4444, in case you need that port for any other process, modify
`start:selenium` and `stop:selenium` commands at `package.js` to use a different
port.

**Note:** In order to run the tests on Chrome and Firefox, you need to have
both browsers installed. Otherwise, modify the Protractor config file located
in `test/conf.js` .

To generate and serve a test report run:

`npm run test:report`

## Reports
It is possible to run dependencies and maintainability reports to better understand the project:

Metrics: `npm run report:metrics` or `npm run report:metrics_server` for server code metrics

Dependencies: `npm run report:dependencies` or `npm run report:dependencies_server` for server dependencies

Any of the mentioned commands will open the generated report in a new browser tab.

## Authentication
For authentication [Google's IAP](https://cloud.google.com/iap/docs/identity-howto) is going
to be used to get the user's identity.

**IMPORTANT**: App will throw HTTP 403's if you do not set the header.

## Using Google's IAP
Google's Identity Aware Proxy adds a custom header `x-goog-iap-jwt-assertion` to all requests containing a JSON Web Token that has the user's Identity.

We follow [Google's article on using JWT with IAP](https://cloud.google.com/iap/docs/signed-headers-howto).

To enable this checking go to your corresponding config file in the `config/` folder (either `production`, `development`, `test` or any other) and set the following variables:

```
"USE_GOOGLE_AUTH": true,
"IAP_AUDIENCE": "/projects/PROJECT_NUMBER/apps/PROJECT_ID",
"IAP_ISSUER_URL": "https://cloud.google.com/iap",
"IAP_PUBLIC_KEY_DICT": "https://www.gstatic.com/iap/verify/public_key",
"IAP_VALID_ALGORITHMS": ["ES256"]
```

To get the `IAP_AUDIENCE` value you can go to the Cloud Platform Console, then to the [Identity-Aware Proxy settings](https://console.cloud.google.com/iam-admin/iap/) for your project, click **More** next to the Load Balancer resource, and then select **Signed Header JWT Audience**. The **Signed Header JWT** dialog that appears displays the audience claim for the selected resource.

For staging we're using `"IAP_AUDIENCE": "/projects/662698939847/apps/shape-163518"`

## Simulated Authentication
To simulate any environment other than production we use a custom header `x-shape-authenticated-user-id` that contains the User ID. If we want to test some predefined profiles we can use any of the user ids we have listed [here](https://github.com/wizeline/shapeconnect/blob/development/doc/emulator.md#test-data-structure).

You can use any tool you like to set/modify the request headers, like [ModHeader for Chrome](https://chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj?hl=en).
