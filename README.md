# Containers from Scratch in Go
This is a container build from scratch in a few lines of Go code. It uses namespaces and cgroups and mounts a tmpfs that's isolated from host filesystem. The project must be run in a Linux environment (GOOS=linux). It is based on the great talk from Liz Rice - [Containers From Scratch](https://www.youtube.com/watch?v=MHv6cWjvQjM&t=1316sc)

## Installation
- Run `make ubuntufs` to create a ubuntu filesystem (Docker required)
- Run `make run` to start the container

## Installation (vagrant)
- Run `make ubuntufs` to create a ubuntu filesystem (Docker required)
- Run `vagrant up`
- Run `vagrant ssh`
- Change to the synchronization folder with `cd container`
- Run `make run` to start the container  

## How it works
- Fork itself with `CLONE_NEWUTS`, `CLONE_NEWPID`, `CLONE_NEWNS` flags with isolated hostname, processes and mounts
- The forked process will create `cgroup` to limit pids to max 20
- Mount `./ubuntu_fs` directory as root filesystem using `chroot` to limit access to host machine's filesystem
- Mount `/mytemp` directory as tmpfs. Any change made to this directory will not be visible from host.
- Mount proc (where `CLONE_NEWPID` namespace was already set) so that container can run `ps` and see only the processes running inside it.
- Execute the supplied argument `/bin/bash` inside the isolated environment

## Testing
You can run a fork bomb `:(){ :|:& };:` in the container to test the cgroup limitation. WARNING! These examples may crash your computer if executed.
