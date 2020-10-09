# Release Guide

This guide describes the process to create a GitHub Release for this project.

1. [Create a release branch](#create-a-release-branch)
1. [Update release branch with versioning changes](#update-release-branch-with-versioning-changes)
1. [Create and push a Git tag](#create-and-push-a-git-tag)
1. [Add release notes](#add-release-notes)
1. [Create a Helm chart release](#create-a-helm-chart-release)
1. [Announce the new release](#announce-the-new-release)
1. [Merge release branch into main](#merge-release-branch-into-main)

## Create a release branch

Identify the base commit in the `main` branch for the release and cut a release branch off `main`.
```console
$ git checkout -b release-<version> <commit-id> # ex: git checkout -b release-v0.4 0d05587
```

Push the release branch to origin.
```console
$ git push origin release-<version> # ex: git push origin release-v0.4
```

## Update release branch with versioning changes

Create a pull request against the release branch `release-<version>` from your fork to update version
information for the Helm charts and default container images. The pull request should do the following:

* Update the container image tag in [charts/osm/values.yaml](/charts/osm/values.yaml) to point to the new release
* Bump the chart and app version in [charts/osm/Chart.yaml](/charts/osm/Chart.yaml) to point to the new release
* Update the default osm-controller image tag in [osm cli](/cmd/cli/install.go) to point to the new release

The pull request must be reviewed and merged before proceeding to the next step.

## Create and push a Git tag

Once the release is ready to be published, create and push a Git tag from the release branch in
the main repo (not fork).

```console
$ export RELEASE_VERSION=<release-version> # ex: export RELEASE_VERSION=v0.4.0
$ git tag -a "$RELEASE_VERSION" -m "<add description here>"
$ git push origin "$RELEASE_VERSION"
```

A [GitHub Action](/.github/workflows/release.yml) is triggered when the tag is pushed.
It will build the CLI binaries, publish a new GitHub release,
upload the packaged binaries and checksums as release assets,
and build and push Docker images for OSM and the demo to the
[`openservicemesh` organization](https://hub.docker.com/u/openservicemesh) on Docker Hub.

## Add release notes

In the description section of the new release, add information about feature additions, bug fixes,
and any other administrative tasks completed on the repository.

## Create a Helm chart release

* create a Git tag from main in the format `chart/x.x.x` where `x.x.x` is the chart version
* push the Git tag to origin
* GitHub Actions runs [helm-gh-pages](https://github.com/stefanprodan/helm-gh-pages) that packages the charts, updates `index.yaml` and pushes the changes to `gh-pages` branch
* GitHub Pages publishes the new version, making the chart available at `https://openservicemesh.github.io/osm`

## Announce the new release

Make an announcement on the [mailing list](https://groups.google.com/g/openservicemesh).

## Merge release branch into main

Open a pull request from the release branch in the main repo (not fork) against the `main` branch.
This should be done directly on Github by going to https://github.com/openservicemesh/osm/compare
and opening a pull request from the release branch `release-<version>`.

Always use the `Merge pull request` option once this pull request is ready for merge since this
pull request must be a merge instead of a squash.