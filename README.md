# sfdx-jenkins-package sample

For a fully guided walkthrough of setting up and configuring continuous integration using scratch orgs and Salesforce CLI, see the [Continuous Integration Using Salesforce DX](https://trailhead.salesforce.com/modules/sfdx_travis_ci) Trailhead module.

This repository shows one way you can successfully set up Salesforce DX to create new package versions with Jenkins. We make a few assumptions in this README. Continue only if you have completed these critical configuration prerequisites.

- You know how to set up your repository with Jenkins. (Need help? See the Jenkins [Getting Started guide](https://jenkins.io/doc/pipeline/tour/getting-started/).)
- You have properly set up JWT-based authorization flow (headless). We recommended using [these steps for generating your self-signed SSL certificate](https://devcenter.heroku.com/articles/ssl-certificate-self). 

## Getting Started

1) Make sure that you have Salesforce CLI installed. Run `sfdx force --help` and confirm you see the command output. If you don't have it installed, you can download and install it from [here](https://developer.salesforce.com/tools/sfdxcli).

2) Set up a [custom tool](https://wiki.jenkins.io/display/JENKINS/Custom+Tools+Plugin) in Jenkins for Salesforce CLI. Name the custom tool `toolbelt` and set its installation directory to the path where the `sfdx` executable is (for example, `/usr/local/bin/sfdx`).

3) [Fork](http://help.github.com/fork-a-repo/) this repo into your GitHub account using the fork link at the top of the page.

4) Clone your forked repo locally: `git clone https://github.com/<git_username>/sfdx-jenkins-package.git`

5) Set up a JWT-based auth flow for the target orgs that you want to deploy to. This step creates a `server.key` file that is used in subsequent steps.
(https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm)

6) Confirm that you can perform a JWT-based auth: `sfdx force:auth:jwt:grant --clientid <your_consumer_key> --jwtkeyfile server.key --username <your_username> --setdefaultdevhubusername`

   **Note:** For more info on setting up JWT-based auth, see [Authorize an Org Using the JWT-Based Flow](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm) in the [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev).

7) From your JWT-Based connected app on Salesforce, retrieve the generated `Consumer Key`.

8) Set up Jenkins [global environment variables](https://wiki.jenkins.io/display/JENKINS/Global+Variable+String+Parameter+Plugin) for your Salesforce `Consumer Key` and `Username`. Note that this username is the username that you use to access your Salesforce org.

    Create an environment variable named `SF_CONSUMER_KEY`.

    Create an environment variable named `SF_USERNAME`.

9) Store the generated `server.key` file as a Jenkins secret file using the [Jenkins Admin Credentials interface](https://wiki.jenkins.io/display/JENKINS/Credentials+Binding+Plugin). Make note of the new entry's ID.

10) Set up Jenkins [global environment variable](https://wiki.jenkins.io/display/JENKINS/Global+Variable+String+Parameter+Plugin) to store the ID of the secret file you created.

    Create an environment variable named `SERVER_KEY_CREDENTALS_ID`.

11) Copy all the contents of `package-sfdx-project.json` into `sfdx-project.json` and save.

12) Create the sample package running this command:

    `sfdx force:package:create --path force-app/main/default/ --name "Jenkins" --description "Jenkins Package Example" --packagetype Unlocked`

13) Create the first package version.

    `sfdx force:package:version:create --package "Jenkins" --installationkeybypass --wait 10 --json --targetdevhubusername HubOrg`

14) In the `Jenkinsfile` file, update the value in the `PACKAGENAME` variable to be the Package ID in your `sfdx-project.json` file.  This id starts with `0Ho`.

15) Commit the updated `sfdx-project.json` and `Jenkinsfile` files.

16) Create a Jenkins pipeline with the `Jenkinsfile` included in the root directory of the Git repository.

Now you're ready to go! When you commit and push a change, your change kicks off a Jenkins build.

Enjoy!

## Contributing to the Repository ###

If you find any issues or opportunities for improving this repository, fix them! Feel free to contribute to this project by [forking](http://help.github.com/fork-a-repo/) this repository and making changes to the content. Once you've made your changes, share them back with the community by sending a pull request. See [How to send pull requests](http://help.github.com/send-pull-requests/) for more information about contributing to GitHub projects.

## Reporting Issues ###

If you find any issues with this demo that you can't fix, feel free to report them in the [issues](https://github.com/forcedotcom/sfdx-jenkins-package/issues) section of this repository.
