## Micromamba 2.8.1 Bug

Previously, I hypothesized that there was [something wrong with Micromamba v2.8.1](baqlava%20and%20humann%20troubleshooting.md). 

So, I tested whether the waafle container could be built (from scratch) on two different machines (locally and dogen) on two different versions (1.8.3 and 2.8.1).

On dogen, I built the container on Podman with micromamba v1.8.3, and it worked. It did not work with version 2.8.1.

Locally, I tried to build the container with Docker using micromamba v1.8.3, and it worked. It did not work with version 2.8.1.

Each time the build failed with a shard error that made micromamba report existing packages (perl, pcre, boost) as missing, just as before
