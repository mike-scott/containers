## Factory Containers

This project allows you to optionally build containers for your Foundries
Factory. Based on the configuration of your factory's `ci-scripts/containers.yml`
definition this have the ability to be multi-arch containers.

This project is a simple template to demonstrate what is possible. As long
as files and directories end with `.disabled`, then nothing will be built.

## How Containers Work
Any top level directory not ending with `.disabled` is treated as a docker
container, and will be built any time a change to this repository is pushed
to the master branch.

In addition to building the container, you may optionally include a
`docker-build.conf` file to add some extra capabilities to your container
CI loop including:

 * **CI testing** - Adding the TEST_CMD="<some command>" directs the CI builder
   to run the command inside the container it just built as a means of
   verifying its functioning correctly.

 * **SKIP_ARCHS** - If doing a multi-arch container build, this will tell the
   CI builder to skip building the container for certain architectures.

 * **MANIFEST_PLATFORMS** - If SKIP_ARCHS is used, then MANIFEST_PLATFORMS
   will need to be updated to reflect what platforms this multi-arch container
   is being published for.

## How Docker Apps Work
Any file ending in `.dockerapp` will be treated as a Docker Application and
be published into your factory's TUF server. The CI logic will be sure to
pin paths to your containers such as "hub.foundries.io/hashcode/shellhttpd:latest"
to become "hub.foundries.io/hashcode/shellhttpd:<gitshort-hash>" so that
the Docker App and target pushed into TUF will be immutable.

After the app is published to TUF, CI logic will create a new TUF target
that includes the previous target's OSTree hash plus the new Docker Applications
that were just published. This will allow all devices running aktualizr-lite
to know that an update is ready to be installed.