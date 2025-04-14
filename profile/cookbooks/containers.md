# Containers vs VMs for application deployment

## Background 
This is a common question that comes up as a project gets ready to deploy an application. And 
this recipe will show when to use one vs. the other, and the implications, and how to do that.

### Virtual Machines
The concept of Virtual Machines are not a new concept, as IBM on the original IBM 360 in the 1960s used
virtual machines as a way to perform multi-user multi-processing, but modern VMs are quite different in
practice than those early implementations.

A virtual machine subsystem, is a part of the OS (or can be a separate tool) that provides an entire emulated
computer+operating system that the application cannot tell is different than a physical computer. On a virtual
machine the software is presented as if it own the hardware in the same way that you can on your physical computer.
The phyical computer is often referred to as "bare metal". The process that creates and controls these VMs are referred
to as Hypervisors or Virtualization Systems, and all major modern CPUs have specific hardware to accelerate this process.

Virtual machines of course aren't magic, and they typically have to devote specific resources to the specific VM. So if 
you have a GPU only one of the VMs can "own" the GPU, now some resources are designed at the hardware level to
be shared, such as the network interface or storage are both designed for many parts of the computer to access simultaneously
so this permits the VMs to share it. Where a VM is unique is that the entire operating system is loaded. So if your program
needs to run under, for example, Windows Server, you can load that on a Virtual Machine with that OS installed. This means
even if your actual computer is running say, Linux, the program inside the VM doesn't think it is in Linux (and in theory
should not even be able to see Linux - _Note, some VM systems permit that information and resources to be shared_)

#### Pros
1. Allows full use of an entire OS, to surround a process, including all the parts of the OS that an application may depend on
2. Use the usual tools to create and manage the VM itself (it truly in this example is a windows computer)
3. The VM itself is as secure as the OS is configured to be, so the windows computer is like any stand-alone windows machine.
4. Resources can be scaled to the application (if you have 24 cores, you can give some number of those to the VM at boot time to support your needs)
5. Everything is convientnly packaged and can be moved in one block
6. The application is isolated from the main OS, so often a VM may be running the same OS, but changes in this OS are not going to affect other VMs or the host
7. VMs emulate entire hardware underpinnings, so for instance if you need to run a x86 on an ARM based computer a VM works well in that case.

#### Cons
1. A very heavy way to surround an application as a full copy of the OS is needed to run a single application
2. Requires an entire OS to be configured just like a bare-metal machine would be
3. May incur costs for another license of the OS (_varies by OS how they deal with VM licensing_)
4. Starting a VM is like booting a machine running that OS (however long it takes to start Windows is how long it takes) 

### Containers
Looking at the cons of VMs, Containers were born as a lightway to wrap an application with a very lightway minimal wrapper with all the resources needed
Containers are designed to be lightweight, simple, movable, quick to start and stop. They also give isolation from the parent but without an entire OS.
Applications still think they are wrapped in some form of OS, but do not believe they are on an entire dedicated machine with an OS. 

Containers are most commonly associated with Docker, a company that promoted the concept of lightweight containers. Their
vision was a way to package an application, any resources the application might need (such as a library) often with a minimal
OS underpinning, but applications are not fully isolated from the host OS, so for instance in the above section, running an 
x86 application won't run on an ARM unless the host OS happens to support x86 emulation. However containers
cxan be built to support multiple architectures at runtime.

The biggest advantage Containers have had in recent years, is the cloud vendors realized you can use them to package a part of the application. Meaning a typical
cloud application is made of various services (such as database, network, etc) and these could be packaged conveniently as a container. This means the service could be
easily moved around with little consequence. After that Google developed a framework that let you define a state you wanted a group of
resources and containers to be in and the software would assure that the state existed. This only worked because the container has
all its own resources. This tooling is known as Kubernetes. Even better if a resource is lost such as a drive failure, or entire CPU failure,
the application could be moved around in a cloud server farm to keep services up despite failures. Even better those states could
include performance so automated load balancing could be spun up quickly during atypical loads without needing further configuration.

#### Pros
1. Lightweight wrapper with applications or resources
2. Rapid spin up/down time
3. Can be programmatically managed via Kubernetes
4. Can be designed to run on multiple architectures (multiarch)
5. Easy to build on your own
6. Typically no licensing since there is no full OS inside
7. Portability

#### Cons
1. The container engine (such as Docker) may require licensing
2. Unless designed so, they only run on one architecture
3. They don't fully emulate the OS and hardware underneath the application
4. Need planning to make the application able to be portable

## Recipe
Let's take a scenario that we've built an application in python, the application is a webservice and uses port 8080. The easiest way to
set up the container to host our application. Since the application is built in python we don't need to worry about the architecture
since python is not a binary compiled application so we just need to make our container to the platorm architecture we plan to run under.
So at GLFHC our servers are x86 based so we will plan on that, as if we set up redundancy for fault-tolerance it is unlikely 
someone is going to sneak an ARM based server into the cluster without our knowledge.

The heart of a container is a specification file that describes our resources we plan to package with the application. To build
our application we will plan to use _docker compose_ a part of the Docker engine that makes containers on the fly through description files.
The heart of a container is the ```compose.yaml``` file . For this recipe we will assume your application is written in the Flask
framework in python. Flask is a lightweight web framework similar to Django and Spring-Boot in Java. Applications in written in
Flask are called "app"

_Note: YAML files use indentation to denote hierarchy (similar to python, so spacing matters!)_

our directory should have the following structure
```text
.

```
[compose.yaml](compose.yaml)
```yaml
services:
  web:
    context: app
    target: builder
  # flask requires a SIGINT to shut itself down properly, so we need to change 
  # docker compose's default SIGTERM message
  stop_signal: SIGINT
  ports:
    - 8080:8080
```
While fairly self explanatory, this will build a container for the flask app '_app_'. The ports take a smidge of explanation,
the first port number is the internal port that the application sees within the container (what your code was written to see), while the
second port is what host port will be assigned. In theory you can take an application that only talks to port 80 internally and have it visible on port 8080 without it knowing.

Now we need to execute our yaml recipe from above:

