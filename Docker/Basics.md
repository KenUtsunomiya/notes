## Container
---
### What is it?

- **Self-contained**
	- each container has everything it needs to function with no reliance on any pre-installed dependencies on the host machine
- **Isolated**
	- containers have minimal influence on the host and other containers, increasing the security of app
- **Independent**
	- each container is independently managed
- **Portable**
	- containers can run anywhere

### Container vs VM
---
### VM

- VMs emulate entire hardware stacks (CPU, memory, storage, network) via a hypervisor, allowing each VM to run its own guest operating system and applications as if on separate physical machines
  
- The hypervisor mediates access to host resources, ensuring complete isolation between VMs and the host

### Container

- Containers share the host OS kernel but isolate processes in user space through namespaces and control groups (cgroups).
  
- A container image bundles only the application layers and dependencies, not a full OS, yielding much smaller image sizes (typically megabytes)


## Image
---
### What is it?

- standardized package that includes all of the files, binaries, libraries, and configurations to run a container
- **Immutable**
	- create a new image or add changes on top of it
- **Composed of layers**
	- each layer represents a set of file system changes that add / remove / modify files

### Image layer

- **Immutable TAR archive** containing filesystem changes relative to the previous layer
	- files added / modified / deleted (deletion only writes deleted files in whiteout files)
	- file permissions / ownership (UID/GID) / timestamps / symbolic links
- Each layer is 



## Registry
---
### What is it?

- centralized location for storing and sharing container images

### Registry vs Repository

![[Pasted image 20250509002355.png]]

