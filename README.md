# Fortify Scan on Pull Request Integration

## Overview
Please see [cdcent/ocio-fortify
README](https://github.com/cdcent/ocio-fortify) for more information
on what Fortify is, and how to scan your source code
manually. Documentation on Fortify can also be found on [Fortify on
docs.cdc.gov](https://docs.cdc.gov/docs/devsecops/services/code-scan/fortify).

This repository acts as a reference example for integrating your
CDCGov repository with Fortify Code Scanning on pull requests. This
functionality exists as two distinct loosely-coupled parts: a workflow
associated with a Github Action Runner that contains the Fortify
scanning utilities (lives in
[cdcent/ocio-fortify](https://github.com/cdcent/ocio-fortify)), and
the workflow contained in this reference example.

CDCGov repositories are designed to be public-facing and open source
by default; it would be irresponsible and insecure to expose a
CDC-hosted Github Action Runner directly to a CDCGov repository. For
this reason, we have created a repository on the CDCEnt organization
to hold the proprietary runtime, and accept specific structured calls
from external repositories like this one. 

The workflow on the CDCEnt side uses a Github Application to
communicate with your pull request in your CDCGov repository you are
integrating with Fortify. This app is called `fortify-pr-scan`
[https://github.com/apps/fortify-pr-scan](https://github.com/apps/fortify-pr-scan). It
should be enabled by default for all repositories in the CDCGov
organization. 

TODO: We may change the authentication schema - documented as it is
for now below.

The trigger-fortify-pr-scan workflow uses a private
key to identify itself as the `fortify-pr-scan` application. The
private key will need to be stored as a Github secret in your
repository; more information below.

## Prerequisites

### Setting up SWA / Fortify server
The team who facilitates this setup is the [CDC Software Assurance
Team](mailto:SoftwareAssurance@cdc.gov). If you are setting up a new
repository for a new CDCGov code project, it is required to set up the
Fortify backend with the SWA team. The code scanner uploads its
findings and artifacts to the central Fortify backend for auditing,
compliance, and record-keeping purposes. 

To request for a new application, go to [this sharepoint
site](https://cdc.sharepoint.com/sites/OCIOSoftwareAssurance) and
submit a "Request New Application Setup" under "Common Forms".

To gain access to an existing application's Fortify reports, ask for
access to it via the the "Request SSC Access" from [this sharepoint
site](https://cdc.sharepoint.com/sites/OCIOSoftwareAssurance). It is
possible to provide a large group of names to onboard to a particular
project.

For more information, see the document [Fortify on
docs.cdc.gov](https://docs.cdc.gov/docs/devsecops/services/code-scan/fortify).

### Fortify App Version ID

Fortify scanning requires a unique Fortify App Version ID. This
Fortify app version ID is obtained during the process for setting up
the Fortify backend server with your project. Your project may involve
multiple fortify app version IDs; save them individually as
`fortify.version` in the top-level directory of the code that belongs
to the microservice.

As an example, if you have a repository called `example` that contains
two microservices, `helloworld` and `foobar`, you will have set up two
subfolders with SWA for the code scans, each with their own Fortify
app version ID. The directory structure would look like the below:

```
./example
.
|-- .github
|   `-- workflows
|       `-- trigger-fortify-scan-pr-template.yml
|-- foobar
|   `-- fortify.version
`-- helloworld
    `-- fortify.version
```

Without the fortify app version ID, the scanning functionality is
severely limited. You will be able to successfully scan for
vulnerabilities, but you will have to check the linked workflow's
output in order to find what the vulnerabilities found actually are.

## Instructions on How to Integrate Your Repository With Fortify PR Scanning

### Check the Github Application Integration

Be sure you can see `fortify-pr-scan` in your repository's Installed
Apps.

The following permissions should already be set for your repository,
given to the `fortify-pr-scan` app. Verify the below:

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

Navigate to the `edso-keyvault` and obtain the secret stored as
`FORTIFY-PR-SCAN-PRIVATE-KEY`. The keyvault is in the
`ocio-edso-dev-moderate-rg` resource group.

The `fortify-pr-scan` private key needs to be set up as a Github
Action secret in your repository in order for the integration to
sucessfully work. Navigate to the `tf-ede-envar-vault` and obtain the
secret stored as `FORTIFY-PR-SCAN-PRIVATE-KEY`. Save the key in your
repository's Github Secrets with the name
`FORTIFY_SCANNER_PRIVATE_KEY`. Note the underscores - the name must
match exactly.

### Set up Pull Request Branch Protections

The main value-added purpose of Fortify code scanning during the Pull
Request process is to prevent the merging of vulrnerable code to the
codebase. For that reason, the Fortify scan-on-pull-request workflow
needs to be able to block PR merges until the code passes the
scan. The workflow registers itself as a status check for each Pull Request
agains the specified target branches.

Additionally, it is a CDC best practice to have all pull requests
reviewed by a human developer, someone who is intimate with your
effort and requirements, as well as the codebase. This in combination
with the Fortify code scanning provides a more robust protection
against vulnerable code making its way into our codebase.

For new repositories created in CDCGov, the default is to block PR
merges until all checks have passed. However, it is essential to
review the setting to confirm. In the settings of your new repository,
navigate to "Branches" under Code and automation. 

For your 'main' branch (and any others you care to branch-protect,
like 'develop'), be sure that "Require a pull request before merging"
and "Require approvals" are selected. Also, select "Require status
checks to pass before merging" and "Require branches to be up to date
before merging" as well.

### Copy the trigger-fortify-scan-pr-template.yml Workflow to Your Repository

The `trigger-fortify-scan-pr-template.yml` is designed to be mostly
complete, and ready-to-use for basic use cases. Depending on the
development cycle rituals and cadence, you & your development team may
deviate from some of the defaults presented in the template. Carefully
review the template and make the change(s) below as necessary.

Copy the `trigger-fortify-scan-pr-template.yml` file found in this
repository to your repository, saved in the `.github/workflows/`
directory. 

Modify the following to suit your needs:
```
pull_request: 
	- branches: modify the branches that will trigger automatic code scanning, when a new Pull Request is opened against it. defaults to "main".
```

## Further Notes
 - [https://github.com/cdcent/ocio-containers/tree/main/openshift-runners/fortify-sca](https://github.com/cdcent/ocio-containers/tree/main/openshift-runners/fortify-sca)
