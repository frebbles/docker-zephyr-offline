# Docker image suitable for stand-alone development, based on zephyr's own docker-image

## Building
This docker image can be built with

```
docker build -t zephyr_doc:v_xxx .
```

## Running zephyr builds

This image can be used for development and building zephyr samples and tests,
for example:

```
docker run -ti -v <local path to zephyr working dir>:/workdir zephyr_doc:v_xxx
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

TODO

## Moving the Docker image

To export:
```docker save -o ./zephyr-build-xxx.tar zephyr_doc:v1```

Can be gzipped quite effectively!
```gzip zephyr-build-xxx.tar```

Move via whatever method you like

To import:
```docker load -i zephyr-build-xxx.tar```

