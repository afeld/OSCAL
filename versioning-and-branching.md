
# OSCAL Versioning

The OSCAL project uses [semantic versioning](https://semver.org/spec/v2.0.0.html) to version releases.

Semantic versions are in the form of `MAJOR.MINOR.PATCH`. Given a version number, increment the:

1. MAJOR version when an incompatible model change is made,
1. MINOR version when a new optional feature is added in a backwards compatible manner, and
1. PATCH version when backwards compatible bug fixes are made.

An incompatible model change will occur when all content produced under a given MAJOR version is no longer schema valid with the new model.

A backwards compatible feature change can entail an addition to a model that adds a new construct, perhaps even deprecating an existing construct, but all previously created content is still schema valid with the new model.

# OSCAL Branching

The main branches of this repository are:
- `main` is the current supported, production-ready release.
- `develop` is the current set of development changes for the next release.
  - This branch can be considered an integration branch where development code can be tested
  - This branch will be merged into `main` when the developed code is ready for release.
- `nist-pages` is the currently deployed website content, which is managed by the CI/CD process.
- `release-*` where `*` matches a MAJOR.MINOR version number.

## Git Setup

To use this strategy, the following Git configuration is needed:

You must do all work in a personal fork of the OSCAL Git repository.

```
git remote add upstream git@github.com:usnistgov/OSCAL.git
```

## Release Branches

A *release branch* is used to stage code in preparation of a new release. Refinements to code can be made in a release branch in preparation for a new release.

Release branches represent production-ready code that is near-ready for release.
- Branched off of `develop`.
- Must be merged back into `develop` as releases are made.
- Must be merged back into `main` to reflect the latest release
- Release branches will be named `release-MAJOR.MINOR`, e.g. release 1.0, release 1.1, release 2.0

### Creating a Release Branch

A release branch can be created by issuing the following Git commands:

```
git checkout -b release-1.2 develop
# TODO: need a method to bump version numbers in metaschemas and content
git commit -a -m "Bumped version number to 1.2"
git push --set-upstream upstream release-1.2
```

### Releasing a Release Branch

Once the release is ready, the release can be made using the following Git commands:

```
git checkout main
git merge --no-ff release-1.2
git tag -a 1.2.0
git push --follow-tags
```

### Releasing a PATCH Revision

Patch releases for a given MAJOR.MINOR version will be marked by annotated tags. This allows the same release branch to be used for multiple PATCH releases.

Once a patch release is ready, the release can be made using the following Git commands:

```
git checkout main
git merge --no-ff release-1.2
git tag -a 1.2.1
git push --follow-tags
```

## Feature Branches

A *feature branch* provides means to integrate a set of features that are a work in progress and the release target of a given set of features is unknown or unpredictable. Work on such a set of features can proceed independent of work targeted at the next release in `develop`.

Feature branches represent major development topics.
- Branched off of `develop`.
- Merged back into `develop` when the feature work is completed.
- Feature branches will be named `feature-*`, where the `*` is a brief dash-separated label describing the feature.

If multiple committers are working on a feature, then each committer must work in a personal branch and submit a PR to the feature branch when their work is complete.

### Creating a Feature Branch

A feature branch can be created by issuing the following Git command:

```
git checkout -b feature-NAME develop
git push --set-upstream upstream feature-NAME
```

where *feature-NAME* will follow the pattern `feature-*`.

### Syncing a Feature Branch with `develop`

It may be necessary to periodically sync a feature branch with the latest in `develop`. You can do this using the following Git commands:

```
# switch to the feature branch
git checkout feature-NAME
# get the latest from upstream
git pull --ff-only upstream feature-NAME
# get the latest from develop
git pull -r upstream develop
git push --force-with-lease upstream feature-NAME
```

### Merging a Feature Branch

The following Git commands will be used to merge a feature branch into `develop`:

```
# switch to the develop branch
git checkout develop
# merge the feature branch
git merge --no-ff feature-myfeature
# delete the feature branch once it is merged
git branch -d feature-myfeature
# push the branch to the upstream repository
git push upstream develop
```

## Personal Working Branches

All individual work will be done in branches in a personal fork of the `upstream` repository.

Personal branches should be named using either:

- `<issue #>-brief-dashed-name` (preferred)
- `wip-brief-dashed-name`

Once work is complete on a personal branch, the branch should be interactively rebased to tidy any commits. Then a PR should be opened against the target `feature-*` branch or the `develop` branch if the changes are to be included in the next release.
