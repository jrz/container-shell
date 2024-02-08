# shell (container-shell)

Starts and attaches a sandboxed shell using docker with access to the current or project directory.

**Warning: I haven't actually tested this with Docker Desktop yet as I use OrbStack**



# Why?
I like using containers to keep a project's dependencies and effects contained.

Sometimes I simply want to have a sandboxed shell when using or testing a tool.

With the recent supplychain attacks this has become more important to me.



# How does it work?
In essence, it starts a container with a keep-alive loop which runs until no terminals are connected by checking the number of /dev/pty/*.

After it has created the container, it immediately starts a shell for it.

The container id is stored in .docker.shell.cid in order to re-connect subsequent shells.
If you start another shell, it will first determine if it needs to start/create the container.

It can read a Shellfile in a project directory to configure things like the docker image.
In order to find the Shellfile or .docker.shell.cid it checks the current dir and all parents.



# Usage
The CLI will most likely change. The program can be used with or without creating a Shellfile:

## Without a Shellfile
```
> shell [docker-image]
```
For example `shell debian:bookworm`

## Using a Shellfile
Create a Shellfile in the current or project directory.
The Shellfile will be sourced to override default settings.
Example:
```
  > vi Shellfile

  DOCKER_PREFIX="sc-myproject"
  DOCKER_OPTS="--platform linux/aarch64"
  DOCKER_IMAGE="debian:bookworm"
  TERM_BG_COLOR="#011279"

  > shell
```



# Misc
I know this is not how docker is intended to work, but this is what I want/need.
 
It uses docker for now, but could also support nixos via OrbStack 'machines' in the future.
See https://github.com/orbstack/orbstack/issues/169

To use the ITERM2_PROFILE setting, create a new profile in iTerm2 with the name "shell-container" (or something else) and change its settings such as  background color.



# Ideas
- It'd be nice to be able to automatically build a Dockerfile when it's in the current directory.
- Add rm command to clean up containers.
- Make the default domain compatible with other things than OrbStack
- Command like shell init / create to create a template Shellfile
- Add PACKAGE_LIST to Shellfile to automatically build a derived docker image
