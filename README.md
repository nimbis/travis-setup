# nifty - *Ni*mbis *f*unctions for .*t*ravis.*y*ml

The script in this repository contains code for the storage of metadata
for Travis builds within a specified git repository. This specified git
repository is often referred to as the "travis-cache"/"cache" repository in
the script and in this README.

This script currently supports the storage and retrieval of the following types of metadata:

* Travis build tagging information.
* Python [coverage](https://coverage.readthedocs.org/en/coverage-4.0.3/) testing information.

## Script Functions

Below is a brief description of the individual functions in this script.
See comments in the script for more detailed information.

### configure_git_authorship
Configure git on Travis using the specified email address and the
`TRAVIS_REPO_SLUG` variable.

### ensure_reqs_and_decrypt
Used to install requirements from NPM/PyPI and setup and decrypt files using
[gitencrypt](https://github.com/shadowhand/git-encrypt)

### args_to_cache_path
Takes the specified arguments and strings them together to construct a
path to a cache object. Arguments longer than 35 characters are split after
the first two characters.

### check_travis_cache
Takes a path to an expected cache object and prints a non-empty string
on a cache hit.

### store_travis_cache
Stores an item in the travis cache. The first argument should be a URL
to store in the cache. All remaining arguments are the key, (and should
match the arguments used later when calling check_travis_cache).

### travis_for_sites

Determines the codepath for the `sites` repository.
For a push to the `sites` repository, we push out corresponding
commits (along with tags) to the site-$SITE repository as well as
site-common. Those pushes will then result in testing and/or
deployment from within the site-$SITE repository, so there's nothing
else we need to do here, (no lettuce tests, no deployment, etc.)

### travis_for_separated_site_repository

Determines the codepath for the `site-*` repositories.
For a push to one of the separated site-$SITE repositories, we
actually perform testing. And, if the tag that was pushed is
named "staging-*" then we also deploy to the staging server.

### travis_for_apps

Determines the codepath for stand-alone Django app repositories.
For a push to a stand-alone Django app repository, we perform
coverage testing. We store the results for the master branch
in the travis-cache repository. We compare current coverage test
results to these store results, and if merging the current branch
would result in a reduction of code test coverage, we fail the build.
This requires that a `make coverage` target exists in the app's Makefile.

## Script variables

The following are the environment variable used in this script and their defaults:

### GIT_CONFIG_EMAIL

Email address that is used to configure git in the `configure_git_authorship` function.
Defaults to `carl.worth+travis@nimbisservices.com`.

### TRAVIS_JOBS_URL_BASE

Base of the URL used in the construction of the `TRAVIS_JOB_URL` variable.

Defaults to `https://magnum.travis-ci.com/${TRAVIS_REPO_SLUG}/jobs`

### TRAVIS_JOB_URL

URL used to store Travis job information in the cache.

Defaults to `${TRAVIS_JOBS_URL_BASE}/${TRAVIS_JOB_ID}`

### TRAVIS_CACHE

Directory where the Travis cache repository will be cloned.

Defaults to `./.travis-cache`

### TRAVIS_CACHE_REPO

Location of the git repository to use as as the Travis cache.
If this repository is private, your .travis.yml file will need to
contain a `source_key` for the clone to be successful.

Defaults to the private repository `git@github.com:nimbis/travis-cache`
