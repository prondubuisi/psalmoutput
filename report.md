# Static Code Analysis Integration for CiviCRM Core
https://summerofcode.withgoogle.com/projects/#6100626637848576

Tags Devops, Developer Happiness,Static Analysis

# Table of Content
1. [Project Overview]()
2. [Project Implementation]()
..* [Testing Psalm Locally]()
..* [Testing Psalm on Remote Server ]()
..* [Testing Psalm with CI/CD tool(Jenkins) on merge]()
..* []()
..* []()
..* []()

# Project Overview

This project aims at integrating Vimeo’s Psalm into the CiviCRM Jenkins toolchain for static
analysis purposes. This project will also work on improving Code coverage report generation for
CiviCRM. Both static analysis and improved unit code test coverage reports will make for better
quality assurance benchmarks for CiviCRM.

The static analysis tool selected for integration with [civi-core](https://github.com/civicrm/civicrm-core) is [Psalm](https://github.com/vimeo/psalm). 
For integrating an open source static analysis tool I considered [Phpstan](https://github.com/phpstan/phpstan), [Phan](https://github.com/phan/phan) and [Psalm](https://github.com/vimeo/psalm). I
choose Vimeo’s Psalm because of its ease of installation, configuration , descriptive error
messages and also its extensive [documentation](documentation.).

[Proposal Link](https://drive.google.com/file/d/1JCqB5w_RPsZbxmrlVHQrRhC0622hRttr/view?usp=sharing)


# Project Implementation

## Phase 1
### Testing Psalm Locally

* I created my own [fork](https://github.com/prondubuisi/civi-mirror) of the Civi-core repository
* I cloned the fork to my Local System
* Tried Running Psalm on the local repository
* Opened [Issues](https://github.com/vimeo/psalm/issues?q=is%3Aissue+author%3Aprondubuisi+is%3Aclosed) in the Psalm Repository to fix errors encountered while running psalm locally

## Phase 2
### Testing Psalm on Remote Server 

## Phase 3
### Testing Psalm with CI/CD tool(Jenkins) on merge

* Setup Jenkins on [remote server](http://35.246.100.181:8080/) provided by the CiviCRM team with help from mentors 
* Installed PHP and Composer on remote Server
* Installed required plugins for Github and Jenkins Integration
* Made Jenkins run Psalm Analysis on pull requests

## Phase 4 
### Testing Psalm with CI/CD tool(Jenkins) on Pull Request

## Phase 5 
### Utilizing Psalm Baseline Feature for Legacy error handling

## Phase 6
### Improving Psalm error output with bootstrap file

## Phase 7
### Fixing Psalm errors

## Phase 3
### Improving Psalm output with Vagrant and Autoloader
1. Can Psalm be Integrated with Civi-core, Jenkins and Github?

## Phase 4
### Fixing Psalm errors

Yes here is a link of a [failing test ](https://github.com/prondubuisi/civi-mirror/pull/11)  with Psalm integrated on a mirror repository 

## P
2. Can Psalm tests pass even with legacy errors?

 Yes here is a link to a [passing test](https://github.com/prondubuisi/civi-mirror/pull/10) with legacy errors in place
 
 See the [Documentation](https://psalm.dev/docs/running_psalm/dealing_with_code_issues/#using-a-baseline-file) for more Information on this
 
 3. Are Psalm errors fixable?
 
 Yes, here is a link to a pull request to show that https://github.com/civicrm/civicrm-core/pull/15043
 
 With the pull request merged, Psalm no longer flags this errors 

# Todo

Continue working with the CiviCRM team until the feature is completely integrated




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
* [Jenkins Server](http://35.246.100.181:8080/job/civicore-pr/configure) with Job and required configuration setup 
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
  
  * Autoloaders May only be feasible 

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


