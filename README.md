# Fortify Scan on Pull Request Integration

## Overview
Please see [cdcent/ocio-fortify
README](https://github.com/cdcent/ocio-fortify) for more information
on what Fortify is, and how to scan your source code manually.

This repository acts as a reference example for integrating your
CDCGov repository with Fortify Code Scanning on pull requests. This
functionality exists as two distinct loosely-coupled parts: a workflow
associated with a Github Action Runner that contains the Fortify
scanning utilities, and the workflow contained in this reference example. 

CDCGov repositories are designed to be public-facing and open source
by default; it would be irresponsible and insecure to expose a
CDC-hosted Github Action Runner directly to a CDCGov repository. For
this reason, we have created a repository on the CDCEnt organization
to hold the proprietary runtime, and accept specific structured calls
from external repositories. 

The workflow on the CDCEnt side uses a Github Application to
communicate with your pull request in your CDCGov repository you are
integrating with Fortify. This app is called `fortify-pr-scan`
[https://github.com/apps/fortify-pr-scan](https://github.com/apps/fortify-pr-scan). It
should be enabled by default for all repositories in the CDCGov
organization. The private key will need to be stored as a Github
secret in your repository; it can be found here #TODO.

## Prerequisites

### Setting up SWA / Fortify server

TODO - i don't know how to do this yet; not in EDSO-525 scope

### Fortify App Version ID

TODO better documentation

Fortify scanning comes with a unique Fortify App Version ID. Steven
can probably explain it better than me; but you will need this.

Without the fortify app version ID, you will be able to successfully
scan for vulnerabilities, but you will have to check the linked
workflow 's output in order to find what the vulnerabilities found actually are. 

## Instructions on How to Integrate Your Repository With Fortify PR Scanning

### Check the Github Application Integration

Be sure you can see `fortify-pr-scan` in your repository's Installed Apps.

You will need to give it the following permissions in order for the Pull Request comment to work:
```
- Actions
  read & write
- Pull Requests
  read & write
- Contents
  read
```

As of the time of this writing, these should be the default
permissions for new repositories in CDCGov. If you are having a "Not
Found" error when trying to "list workflows" on the CDCEnt side, it is
probably the application permissions not being correct.

### Add the fortify-pr-scan Private Key to Your Repository

You'll need this; find it from TODO upload it to where?

The `fortify-pr-scan` private key needs to be set up as a Github
Action secret in your repository in order for the integration to
sucessfully work. Save the key in your secrets with the name
`FORTIFY_SCANNER_PRIVATE_KEY`.

### Set up Pull Request Branch Protections

The whole point of the scan is that it won't let you merge failing
code right? You'll need to set up your repository to not allow merging
of PRs until all PR checks are enabled, and you get a review approval.

In the settings of your new repository, navigate to "Branches" under
Code and automation. For your 'main' branch (and any others you care
to branch-protect, like 'develop'), be sure that "Require a pull
request before merging" and "Require approvals" are selected. Also,
select "Require status checks to pass before merging" and "Require
branches to be up to date before merging" as well.


### Copy the trigger-fortify-scan-pr-template.yml Workflow to Your Repository

Copy the `trigger-fortify-scan-pr-template.yml` file found in this
repository to your repository, saved in the `.github/workflows/`
directory. 

Modify the following to suit your needs:
```
pull_request: 
	- branch: modify the branches that will trigger automatic code scanning, when a new Pull Request is opened against it. defaults to "main".
```

## Further Notes
 - [https://github.com/cdcent/ocio-containers/tree/main/openshift-runners/fortify-sca](https://github.com/cdcent/ocio-containers/tree/main/openshift-runners/fortify-sca)
