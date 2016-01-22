# nifty - *Ni*mbis *f*unctions for .*t*ravis.*y*ml

The script in this repository contains code for the storage of metadata
for Travis builds within a specified git repository. This specified git
repository is often referred to as the "travis-cache"/"cache" repository in
the script and in this README.

This script currently supports the storage and retrieval of the following types of metadata:

* Travis build tagging information.
* Python [coverage](https://coverage.readthedocs.org/en/coverage-4.0.3/) testing information.

## Usage

Sourcing this script clones the cache repository to the location specified
by the [NIFTY_TRAVIS_CACHE_DIR](###NIFTY_TRAVIS_CACHE_DIR) variable. Then you
call one of public functions defined by the script in your `.travis.yml` file
like so:

```
env:
  global:
  - NIFTY_TRAVIS_CACHE_REPO=https://github.com/nimbis/travis-cache-public.git
before_script:
- git clone https://github.com/nimbis/nifty.git ./.nifty
script:
- source ./.nifty/nifty-script
- verify_coverage_improvement
```

## Public Functions

Below is a brief description of the individual public functions defined
in this script. See comments in the script itself for more detailed
information.

### travis_for_sites

This function is for use with the `sites` repository.
For a push to the `sites` repository, we push out corresponding
commits (along with tags) to the site-$SITE repository as well as
site-common. Those pushes will then result in testing and/or
deployment from within the site-$SITE repository, so there's nothing
else we need to do here, (no lettuce tests, no deployment, etc.)

### travis_for_separated_site_repository

This function is for use with the `site-*` repositories.
For a push to one of the separated site-$SITE repositories, we
actually perform testing. And, if the tag that was pushed is
named "staging-*" then we also deploy to the staging server.

### verify_coverage_improvement

We store the coverage results for the master branch in the travis-cache
repository. We compare current coverage test results to these store
results, and if merging the current branch would result in a reduction
of code test coverage, we fail the build.

## Script variables

The following are the environment variable used in this script and their defaults:

### NIFTY_GIT_CONFIG_EMAIL

Email address that is used to configure git in the `configure_git_authorship` function.
Defaults to `carl.worth+travis@nimbisservices.com`.

### NIFTY_TRAVIS_JOBS_URL_BASE

Base of the URL used in the construction of the `NIFTY_TRAVIS_JOB_URL` variable.

Defaults to `https://magnum.travis-ci.com/${TRAVIS_REPO_SLUG}/jobs`

### NIFTY_TRAVIS_JOB_URL

URL used to store Travis job information in the cache.

Defaults to `${NIFTY_TRAVIS_JOBS_URL_BASE}/${NIFTY_TRAVIS_JOB_ID}`

### NIFTY_TRAVIS_CACHE_DIR

Directory where the Travis cache repository will be cloned.

Defaults to `./.travis-cache`

### TRAVIS_CACHE_REPO

Location of the git repository to use as as the Travis cache.
If this repository is private, your .travis.yml file will need to
contain a `source_key` for the clone to be successful.

Defaults to the private repository `git@github.com:nimbis/travis-cache`
