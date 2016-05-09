strider-docker-runner
=====================

## Changes in this fork
Due to [issue #18](https://github.com/Strider-CD/strider-docker-runner/issues/18), I needed to create a work-around to get `npm install` to run prior to `npm test`. This fork uses a [modified version of strider-runner-core](https://github.com/wbio/strider-runner-core) to run `npm install` if the runner sees a non-install npm command before seeing an install command. This is a very hacky fix that probably won't work for everyone, but based on the discussion in the linked issue as well as the discussion in [another issue](https://github.com/Strider-CD/strider-heroku/issues/4#issuecomment-54719228) referenced in that thread, a proper fix sounded like more work than I was willing to take on at the moment.

## Installation

`cd` into strider deployment and run `npm install git+https://github.com/wbio/strider-docker-runner.git`

If you need to install Docker, see the [official installation instructions](https://docs.docker.com/installation/)

The default image is `strider/strider-docker-slave` -- it is recommended to `docker pull strider/strider-docker-slave` directly on the Docker host, however the plugin will do this for you as of [PR#22](https://github.com/Strider-CD/strider-docker-runner/pull/22).

If Docker is running on the same machine as Strider, you do not need to add any additional environment variables -- the plugin will try to use `unix:///var/run/docker.sock` to communicate with Docker. Make sure Strider has permission to do so, otherwise you will get errors when running a build.

If Docker is running on a remote machine, you will need to use the [Docker Remote API](https://docs.docker.com/reference/api/docker_remote_api/) and let Strider know about it by setting `DOCKER_HOST` accordingly. e.g. `DOCKER_HOST=http://127.0.0.1:4243 strider` You can also override this value in the runner config per-project branch.

Once Strider is running, go to your project's plugin config page and you will be able to select the Docker runner. You can also configure the Runner to use a different, custom base image. You may even combine this feature with [strider-docker-build](https://github.com/Strider-CD/strider-docker-build) to fully automate changes to the base image.

## Configuration Environment Variables

It uses the standard Docker environment variable `DOCKER_HOST`

Examples:

```
DOCKER_HOST="http://127.0.0.1:4243"
DOCKER_HOST="unix:///var/run/docker.sock"
DOCKER_HOST="tcp://127.0.0.1:4243"
```

You are not required to set `DOCKER_HOST` globally. You may choose to configure this value per project through the plugin config page.

If DOCKER_HOST is not set and the value is also not configured in the plugin config page, it defaults to `unix:///var/run/docker.sock`

If you are working with remote docker containers, it is advised to use [Dockers TLS security options](http://docs.docker.com/articles/https/). For client setup please see the [official documentation](http://docs.docker.com/articles/https/#client-modes)

Example
```
DOCKER_HOST="tcp://127.0.0.1:4243"
DOCKER_TLS_VERIFY=1
DOCKER_CERT_PATH=~/stridercd/certs
```
Be sure to [enable TLS on the Daemon](http://docs.docker.com/articles/https/#daemon-modes) as well. More information at http://docs.docker.com/articles/https/

## Verification

See this comment in the "prepare" phase telling you that **docker is alive**

![](https://cloud.githubusercontent.com/assets/112170/3838066/871cff0c-1dfc-11e4-9fce-430447bafffa.png)
