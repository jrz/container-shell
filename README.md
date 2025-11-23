# shell (container-shell) v0.2.4

Starts and attaches a sandboxed shell using docker with access to the current or project directory.
It could also be described as chroot mixed with containers.

Please visit https://github.com/jrz/container-shell for the latest version.


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



# Installation
There is no installer or package yet. Simply copy the shell script to any directory in your PATH.
Make sure that the executable bit is set. For example:
```
cp shell /usr/local/bin/
chmod +x /usr/local/bin/shell
```

# Updating
To check for the latest version, use the following command:
```
shell update
```


# Usage
The CLI will most likely change. The program can be used ad-hoc or using a Shellfile:

## Ad hoc (no Shellfile)
```
> shell [docker-image]
```
For example `shell debian:trixie`

## Using a Shellfile
Use the ```shell init``` command to create a new Shellfile in the current directory if no other Shellfile can be found. The Shellfile will be sourced to override default settings. Please refer to the 'Shellfile settings' section for all available settings.

```
  > shell init         # Creates a Shellfile with a debian:trixie image
  > vi ./Shellfile     # Optionally edit the Shellfile
  > shell              # Run the container
```



## Shellfile settings
Example:
```
  # Docker image to use when creating the container (debian:trixie)
  DOCKER_IMAGE="debian:trixie"

  # Naming prefix  (ex. shellcontainer-myproject)
  DOCKER_PREFIX="sc-myproject"
  
  # Passed when creating container (ex. --platform linux/aarch64)
  DOCKER_OPTS="--platform linux/aarch64"
  
  # Temporary terminal background color when inside container (#011279)
  TERM_BG_COLOR="#011279"
  
  # Temporary iTerm2 profile name when inside container (container-shell)
  MOUNTPOINT="/project"
```



# Misc
I know this is not how docker is intended to work, but this is what I want/need.
 
It uses docker for now, but could also support nixos via OrbStack 'machines' in the future.
See https://github.com/orbstack/orbstack/issues/169

To use the ITERM2_PROFILE setting instead of using only background colors, create a new profile in iTerm2 with the name "shell-container" (or something else). Change its settings such as background color.

The bindmount fails when the project directory is moved after creation.


# Ideas
- It'd be nice to be able to automatically build a Dockerfile when it's in the current directory.
- Add rm command to clean up containers.
- Make the default domain compatible with other things than OrbStack
- Add PACKAGE_LIST to Shellfile to automatically build a derived docker image
- Add an interactive ui for ```shell init```
