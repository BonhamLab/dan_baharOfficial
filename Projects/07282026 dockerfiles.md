## Dockerfiles Overview

A Dockerfile is the recipe for building a container, and you keep this recipe in github. On whatever machine has the file, you can build and run an image based on it. Both Podman and Docker can read the same Dockerfile.

## Drafting the yaml

Before proceeding to make a Dockerfile, you must first create a [yaml file](waafle_env.yaml), with all of the environment’s ingredients. While dogen can have multiple environments, like waafle_env and baqlava_env, the container only has one environment.Because the container only has one environment, we name it "base". Of note, you can name the environment whatever you want on github, but we will be changing its name to base later.

## Drafting the Dockerfile

Below is the waafle Dockerfile (called [waafle143_dockerfile](waafle281_dockerfile), for micromamba 1.4.3). Most Dockerfiles use the same standardized 5-line structure (a Baqlava dockerfile needs a sixth line `RUN pip install baqlava`, because pip can be a bit weird).

```
FROM mambaorg/micromamba:1.4.3
COPY --chown=$MAMBA_USER:$MAMBA_USER waafle_env.yaml /tmp/env.yaml
RUN micromamba install -y -n base -f /tmp/env.yaml
ARG MAMBA_DOCKERFILE_ACTIVATE=1
RUN waafle_search --help
```

`FROM mambaorg/micromamba:1.4.3` When making the container, use package manager micromamba 1.4.3.

`COPY --chown=$MAMBA_USER:$MAMBA_USER waafle_env.yaml /tmp/env.yaml` Copies your waafle_env.yaml file into the container under the name env.yaml. /tmp/env.yaml = where it lands inside the container; --chown=$MAMBA_USER:$MAMBA_USER = hands the file to the container’s normal user, as opposed to the admin. Do not put your actual username into this command, keep the $MAMBA_USER:$MAMBA_USER.

`RUN micromamba install -y -n base -f /tmp/env.yaml` Install the packages. -y = yes to all prompts; -n base = into the "base" environment; -f /tmp/env.yaml = using the yaml from above.

`ARG MAMBA_DOCKERFILE_ACTIVATE=1` Switch the base environment ON for the line below.

`RUN waafle_search --help` Try running waafle to ensure that it is actually installed.
