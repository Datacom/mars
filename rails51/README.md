# MARS Rails 5.1 Stack

This Readme covers the setup for a Rails 5.1 app, and packaging with Docker

## Libraries

Unless changed to meet a specific requirement, a 5.1 Rails app will be using
- Ruby 2.4
- Node 6 LTS
- Postgres 9.6 (don't go past the versions that AWS installs)
- Yarn

It may or may not be using Webpack + React.  If it does, use the Core Rails 5.1 features.

## Docker

The Dockerfile is set up to install all the correct versions on an Ubuntu base.

The Dockerfile expects to be given BUILD_DATE and GIT_REF as arguments, so they appear in the image metadata.

For Development you can use the `docker-compose` to bring up all services


## Building

Recommend adding the following to your `.bash_aliases` so the ARGs are supplied automatically

```sh
alias dbuild='docker build --build-arg BUILD_DATE=`date -u +"%Y-%m-%dT%H:%M:%SZ"` --build-arg GIT_REF=`git rev-parse --short HEAD` '
```