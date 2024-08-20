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

## Instructions on How to Integrate Your Repository With Fortify PR Scanning

### Check the Github Application Integration

Be sure you can see `fortify-pr-scan` in your repository's Installed Apps.

### Add the fortify-pr-scan Private Key to Your Repository

You'll need this; find it from TODO upload it to where?

### Set up Pull Request Merge Protections

The whole point of the scan is that it won't let you merge failing
code right? You'll need to set up your repository to not allow merging
of PRs until all PR checks are enabled, and you get a review approval.

### Copy the trigger-fortify-scan-pr-template.yml Workflow to Your Repository

TODO here are the parts you need to fill out:
 inputs
 etc
 go
 here

## Further Notes
 - [https://github.com/cdcent/ocio-containers/tree/main/openshift-runners/fortify-sca](https://github.com/cdcent/ocio-containers/tree/main/openshift-runners/fortify-sca)
