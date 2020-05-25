# Docker image suitable for stand-alone/offline development, based on zephyr's own docker-image

Much of the zephyr installation and updates system incorporates a lot of pulling information 
from various repositories from the internet in order to complete a full build setup. 
On systems where hardware is being developed with security in mind, air gaps, or limited 
internet access hand severely hinder being able to setup a proper build environment without 
having all repositories available.

Instead of having to transfer a full Virtual Machine, or attempt a full installation on a 
non internet connected system (welcome to dependency hell), this zephyr docker image 
intends to enable building and debugging a zephyr application without requiring an internet 
connection.

Also, this concept can be used to incorporate into a CI system. Or used as a basis for other
complex build environments!.

Based off the [Zephyr Docker image on the Zephyr-rtos github repository](https://github.com/zephyrproject-rtos/docker-image)

## Building
Before building, ensure that the entrypoint.sh has execute persmissions!

```chmod ug+x entrypoint.sh```

This docker image can be built with the following

```
docker build -t zephyr_doc:v_xxx .
```

## Running zephyr builds
The intended operation is that on your local machine you would have a Zephyr application or working directory,
this would be the <local path to zephyr working dir> and would be mapped using the command below 
to /workdir in the docker container. All compiles/builds would then be kept persistent in your local
machine environment and the build environment would remain unchanged.

This image can be used for development and building zephyr applications, samples and tests,
for example (--privileged required for USB port access/flashing):

```
docker run -ti --privileged -v <local path to zephyr working dir>:/workdir zephyr_doc:v_xxx
```

Then, follow the steps below to build a sample application:

```
cd samples/hello_world
mkdir build
cd build
cmake -DBOARD=nucleo_f746zg ..
make run
```

we can also run based off west tool as per zephyr's getting started

```
west build -p auto -b nucleo_f746zg /zephyrproject/zephyr/samples/basic/blinky
west flash
```

## Toolchain variants

We have two toolchains installed as a part of this image:
- Zephyr SDK
- GNU Arm Embedded Toolchain

To switch, set ZEPHYR_TOOLCHAIN_VARIANT, [zephyr|gnuarmemb]

## Displays/VNC

Further it is possible to run _native POSIX_ samples that require a display
and check the display output via a VNC client. To allow the VNC client to
connect to the docker instance port 5900 needs to be forwarded to the host,
for example:

```
docker run -ti -p 5900:5900 -v <local path to zephyr working dir>:/workdir zephyr_doc:v_xxx
```

Then, follow the steps below to build a display sample application for the
_native POSIX_ board:

```
cd samples/display/cfb
mkdir build
cd build
cmake -DBOARD=native_posix -GNinja ..
ninja run
```

The result can be observed by connecting a VNC client to _localhost_ at port
_5900_, the default VNC password is _zephyr_.

For example on a Ubuntu host system:

```
vncviewer localhost:5900
```

## Programming/Debugging/Flashing

Programming with 

```west flash```

is working, but you will likely require adding an appropriate udev rules file that will give
docker permissions to access the device, also requiring the --privileged docker run option.

An example for an ST-Link V2 exists as 66-stlink-rule.rules and can be installed/added by:

```sudo cp 66-stlink-rule.rules /etc/udev/rules.d/```

## Moving the Docker image

To export:
```docker save -o ./zephyr-build-xxx.tar zephyr_doc:v1```

Can be gzipped quite effectively!
```gzip zephyr-build-xxx.tar```

Move via whatever method you like

To import:
```docker load -i zephyr-build-xxx.tar```

