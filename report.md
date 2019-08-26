# Static Code Analysis Integration for CiviCRM Core
https://summerofcode.withgoogle.com/projects/#6100626637848576

Tags Devops, Developer Happiness,Static Analysis

# Table of Content
1. [Project Overview](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#project-overview)

2. [Project Implementation](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#project-implementation)

* [Analyzing Civi-core with Psalm Locally](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#analyzing-civi-core-with-psalm-locally)

* [Analyzing Civi-core with Psalm on Remote Server](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#analyzing-civi-core-with-psalm-on-remote-server)

* [Analyzing Civi-core with Psalm and CI/CD tool(Jenkins) on merge](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#analyzing-civi-core-with-psalm-and-cicd-tooljenkins-on-merge)

* [Analyzing Civi-core with Psalm and CI/CD tool(Jenkins) on Pull Request](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#analyzing-civi-core-with-psalm-and-cicd-tooljenkins-on-pull-request)

* [Utilizing Psalm Baseline Feature for Civi-core Legacy code error handling](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#utilizing-psalm-baseline-feature-for-civi-core-legacy-code-error-handling)

* [Improving Civi-core Psalm error output with bootstrap file](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#improving-civi-core-psalm-error-output-with-bootstrap-file)

* [Fixing Civi-core errors as detected by Psalm](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#fixing-civi-core-errors-as-detected-by-psalm)

3. Documentation
*
*
*

# Project Overview

This project aims at integrating Vimeo’s Psalm into the CiviCRM Jenkins toolchain for static
analysis purposes. This project will also work on improving Code coverage report generation for
CiviCRM. Both static analysis and improved unit code test coverage reports will make for better
quality assurance benchmarks for CiviCRM.

The static analysis tool selected for integration with [Civi-core](https://github.com/civicrm/civicrm-core) is [Psalm](https://github.com/vimeo/psalm). 
For integrating an open source static analysis tool I considered [Phpstan](https://github.com/phpstan/phpstan), [Phan](https://github.com/phan/phan) and [Psalm](https://github.com/vimeo/psalm). I
choose Vimeo’s Psalm because of its ease of installation, configuration , descriptive error
messages and also its extensive [documentation](documentation.).

[Proposal Link](https://drive.google.com/file/d/1JCqB5w_RPsZbxmrlVHQrRhC0622hRttr/view?usp=sharing)


# Project Implementation

## Phase 1
### Analyzing Civi-core with Psalm Locally

During the Community bonding phase the CiviCRM team provided a remote server for use by me and my mentors for this project implementation. Though a remote server was provided my mentors made me understand that Static analysis would have an easier workflow if I tried Psalm out Locally on my Computer. To achieve local Static analysis I did the following;

* I created my own [fork](https://github.com/prondubuisi/civi-mirror) of the Civi-core repository
* I cloned the fork to my Local System
* Tried Running Psalm on the local repository
* Opened [Issues](https://github.com/vimeo/psalm/issues?q=is%3Aissue+author%3Aprondubuisi+is%3Aclosed) in the Psalm Repository to fix errors encountered while running psalm locally

From Beginning it was clear there was no way I would implement my static analysis project  directly on the CiviCRM Jenkins Tool chain as the infrastructure  was used on daily basis for active development, hence I was provided with a server having similar configurations.

## Phase 2
### Analyzing Civi-core with Psalm on Remote Server 

The developers maintaining Psalm were very quick to fix errors encountered while running Psalm on Civi-core,after the errors where fixed and Psalm was confirmed to work locally I tried Static Analysis on the remote server by doing the following;

* I installed PHP on the server
* I installed Composer on the server
* I Installed git on the server
* I cloned the Civi-core repository to the remote server
* I installed Psalm via Composer as one of Civi-core Dependencies

With all the requirements in place, I was able to run Psalm static analysis on Civi-core without errors. One important observation from this was that Psalm returned a varying error(locally and remotely). Mentors from the CiviCRM community mentioned that this was normal and that was the reason CiviCRM had test suites for different environments and different PHP versions.

## Phase 3
### Analyzing Civi-core with Psalm and CI/CD tool(Jenkins) on merge

The CiviCRM CI/CD Tool Chain ran on Jenkins, so I was required to try implementing Civi-core Psalm Static Analysis with
Jenkins. To achieve that I did the following 


* Setup Jenkins on [remote server](http://35.246.100.181:8080/) provided by the CiviCRM team with help from mentors 
* Configure Jenkins to work with Github
* Configure Github webhooks to work with Jenkins server url endpoint
* Attempt code merge to test integration

Having figured that Psalm worked with github merge, the next step was working to run Psalm  whenever a pull request is made to the Civi-core mirror repository.

## Phase 4 
### Analyzing Civi-core with Psalm and CI/CD tool(Jenkins) on Pull Request

* Install Jenkins Git plugin
* Install Jenkins Github plugin 
* Install Jenkins GitHub Pull Request Builder
* Install Jenkins Rebuilder plugin
* Configure Github webhooks to work with Jenkins server url endpoint
* Attempt [pull request](https://github.com/prondubuisi/civi-mirror/pull/6) to test integration

Here is the [output](http://35.246.100.181:8080/job/civicore-pr/74/console) from the above pull request, notice that even though Psalm static analysis completes it returns with many errors. This is expected as there are lots of legacy code within the codebase.

## Phase 5 
### Utilizing Psalm Baseline Feature for Civi-core Legacy code error handling

Even though the error output from Psalm Static analysis was appreciated, there was need to handle legacy code errors with an approach that keeps them in check, while allowing for focus errors  resulting from new Pull requests. This was necessary because it was not feasible for the CiviCRM development team to fix all the errors already existing within the codebase(Thousands of error) before utilizing the benefits of Psalm static analysis for new pull requests. Most of this errors did not cause a breakdown in functionality and the dev team had both feature requests and bugs breaking CiviCRM functionality to handle.

Psalm had a baseline feature which allows it to grandfather existing errors while picking up new errors from new pull requests. This was exactly what was needed. With the Baseline feature implemented, Psalm was able to analyze Civi-core without errors. It is worthy of note that CiviCRM Psalm legacy errors where marked for incremental fixing.

Here is passing [Pull request](https://github.com/prondubuisi/civi-mirror/pull/10) and its [output](http://35.246.100.181:8080/job/civicore-pr/72/console)  with the baseline feature Implemented.

## Phase 6
### Improving Civi-core Psalm error output with bootstrap file

With Civi-core Psalm static analysis integrated on pull requests, it was time to take a closer look at the error output, my mentors were able to dectect that most of the errors reported by Psalm were false negatives. This was the case because The Classes/Methods not found errors from Psalm pointed to Classes and functions that were found in the Civi-core codebase.

The CiviCRM team faced a similar issue when integrating PHPUnit as part of their CI/CD Jenkins tool chain so the created a [Bootstrap](https://github.com/civicrm/civicrm-core/blob/master/tests/phpunit/CiviTest/bootstrap.php) file to pull in dependcies(classes and functions) required by Civicore. Psalm had an `Autoloader` directive for cases like this and the directive was utilized in solving this issue.

Adding the Bootstrap file through the autoloader directive eliminated the false Classes/Functions not found errors. See more information on the implementation in the [documentation](https://github.com/prondubuisi/psalmoutput/blob/master/report.md#documentation-on-integrating-civicrm-core-with-jenkins-and-github) Section.

## Phase 7
### Fixing Civi-core errors as detected by Psalm 

An Important part of this project was determining if errors detected by Psalm static analysis were fixable. To assetain this
we analysed a [section](https://github.com/civicrm/civicrm-core/tree/master/api/v3/examples) of Civi-core codebase and I submitted [pull request](https://github.com/civicrm/civicrm-core/pull/15043) to fix the errors detected. Merging the pull request to the codebase eliminated the errors on second static analysis.
 

# Todo

* Continue working with the CiviCRM team to Integrate Psalm as part of CI/CD Tool chain
* Fix more Legacy Codebase errors dectected by Psalm


# Documentation on Integrating CiviCRM core with Jenkins and Github

## Installing  Jenkins on a server

*  Install PHP on server, check out this [link](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-ubuntu-18-04)
*  Install composer on server, check out this [link](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-18-04)
*  Install Jenkins on Server, check out this [link](https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-18-04)

## Integrating Jenkins with Github
*  [Here ](http://www.inanzzz.com/index.php/post/ljgv/setup-github-and-jenkins-integration-for-pull-request-builder-and-merger)is a concise guide on setting up Jenkins to Listen for pull requests on a Github Repo

## Psalm Setup

The steps from the above help you set up a Jenkins Job, And also Configure the Job, Now you can Add A build Step so Jenkins can install required Dependencies for Psalm, as well as run psalm static analysis on civicore



Add the following to build Step

*  `composer update` 
*  `composer require --dev vimeo/psalm:3.4.9`
*  `./vendor/bin/psalm --show-info=false`


**Configuring Psalm to Work with civicore**

* Add a psalm.xml file to civicore root folder
   *  This can be achieved by running `./vendor/bin/psalm --init`
* Add psalmbase.xml file to civicore root folder, this is a baseline file
   *  This can be achieved by running `./vendor/bin/psalm --set-baseline=psalmbaseline.xml`

Psalm [baseline](https://psalm.dev/docs/running_psalm/dealing_with_code_issues/#using-a-baseline-file) feature allows us to grandfather errors already existing in civicore, while checking for new errors arising from subsequent PR's after its installation. 

## Samples
* [Build History for Mirror Repository](http://35.246.100.181:8080/job/civicore-pr/) 

* [Github repo](https://github.com/prondubuisi/civi-mirror) Mirrowing civicore, notice the [psalm.xml](https://github.com/prondubuisi/civi-mirror/blob/master/psalm.xml) file and the [psalmbaseline.xml](https://github.com/prondubuisi/civi-mirror/blob/master/psalmbaseline.xml) file


## Potential Gotchas 

* For consistent results, It is advisable to create psalm configuration files i.e psalm.xml && psalmbaseline.xml on the actual server where Jenkins is installed, as Psalm might have different error count when run on the same repository, with same configurations but on different servers. This is the case because different systems have different configurations. This is the reason why Civi-core exists for different versions of PHP for example

* running `./vendor/bin/psalm --init` creates a psalm.xml file which lists the different folders in the current repository, for civicore folders ext,packages are always included but non existent, do well to remove the folders from psalm.xml before running `./vendor/bin/psalm`  on the folder, to avoid errors

# Handling False negatives with an Autoloader File

False Negatives refer to  error pointers that are actually incorrect, this was the case with Psalm reporting that many classes and dependencies were missing when in fact they were present example e.g. Undefined Function/Class errors. To make up for this the Auto loader(Bootstrap) [file](https://github.com/civicrm/civicrm-core/blob/master/tests/phpunit/CiviTest/bootstrap.php) for Civi-core Unit tests was used for Psalm by adding an autoloader in the `psalm.xml` file

`<psalm  autoloader="tests/phpunit/CiviTest/bootstrap.php"  > `

and the observations are as follows

* For Autoloaders to work the Civi-core repository has to be a working repository with CiviCRM tools like civibuild in place and civicrm.settings.php file properly updated. This can be acheieved using a [Vagrant installation](https://github.com/civicrm/civicrm-buildkit-vagrant) of CiviCRM. 

* Running analysis with Psalm and an Autoloader
 usually returns different errors and error count as compared to running without an autoloader
 
 * Running analysis with Psalm and an Autoloader takes lots of hours when run on the entire Civi-core repository,
  without the autoloader this analysis works in minutes
  
  

# Handy Flags for PSALM
* --threads=10
 .* This increases analysis speed as it utilizes 10 threads instead of one
 
* --show-info=false

 .* Ensures that only error messages are returned by Psalm and not info warnings, this keeps the output clean

* --debug-by-line

.* Very useful to trace line causing Psalm to fail, this makes fixing easy

* --config=psalm.xml

.* Set path to Psalm.xml in case you intend to put the config file outside the root directory

# Blog posts
* [Community Bonding](https://medium.com/@ndubuisionyemenam/gsoc-community-bonding-with-civicrm-427f253afa76?source=your_stories_page---------------------------)
* [First Evaluation](https://medium.com/studevs/google-summer-of-code-first-evaluation-a89d69d55845?source=your_stories_page---------------------------)
* [Second Evaluation](https://medium.com/studevs/google-summer-of-code-second-evaluation-7382813d5c5d?source=your_stories_page---------------------------)


