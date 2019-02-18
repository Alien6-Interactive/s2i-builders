# S2I Builders Catalog
This Catalog contains various s2i builders that have been used by Alien6 people to faster Docker Images builds from sources.

## Source-To-Image (S2I)
### Overview
Source-to-Image (S2I) is a toolkit and workflow for building reproducible Docker images from source code. S2I produces
ready-to-run images by injecting source code into a Docker container and letting the container prepare that source code for execution. By creating self-assembling **builder images**, you can version and control your build environments exactly like you use Docker images to version your runtime environments.

### Anatomy of a builder image

Creating builder images is easy. `s2i` looks for you to supply the following scripts to use with an
image:

1. `assemble` - builds and/or deploys the source
1. `run`- runs the assembled artifacts
1. `save-artifacts` (optional) - captures the artifacts from a previous build into the next incremental build
1. `usage` (optional) - displays builder image usage information

Additionally for the best user experience and optimized `s2i` operation we suggest images
to have `/bin/sh` and `tar` commands available.

### Usage

```s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-alpine-golang/e2e/ alien6/s2i-alpine-golang:0.1 golang-sample-app```
