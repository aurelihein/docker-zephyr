# docker-image


## How to use it with zephyr :

Start the docker :

```
mkdir ~/somewhere
cd ~/somewhere
docker run --rm -it -v ${PWD}:/workdir aurelienbouin/docker-zephyr:ubuntu_18.04-zsdk_0.10.3
```

Now you are inside the docker

Here is how to get zephyrproject and compile it for nrf52DK board :

```
west init zephyrproject
cd zephyrproject
west update
cd zephyr
git checkout -b mybranch_v2.1.0-rc1 v2.1.0-rc1
source zephyr-env.sh
west build -b nrf52_pca10040 -d build_mybranch_v2.1.0 samples/bluetooth/hci_uart
```

The output hex file inside the docker is in :

```
ls -l build_mybranch_v2.1.0/zephyr/zephyr.hex
```

The output hex file from outside the docker is in :

```
ls -l ~/somewhere/zephyrproject/zephyr/build_mybranch_v2.1.0/zephyr/zephyr.hex
```



This docker image can be built with

```
docker build -t docker-zephyr:ubuntu_18.04-zsdk_0.10.3 .
```

and can be used for development and building zephyr samples and tests,
for example:

```
docker run -ti -v <path to zephyr tree>:/workdir docker-zephyr:ubuntu_18.04-zsdk_0.10.3
```

Then, follow the steps below to build a sample application:

```
cd samples/hello_world
mkdir build
cd build
cmake -DBOARD=qemu_x86 ..
make run
```

The image is also available on docker.io, so you can skip the build step
and directly pull from docker.io and build:

```
docker run -ti -v $HOME/Work/github/zephyr:/workdir aurelienbouin/docker-zephyr:ubuntu_18.04-zsdk_0.10.3
```

The environment is set and ready to go, no need to source zephyr-env.sh.

We have two toolchains installed:
- Zephyr SDK
- GNU Arm Embedded Toolchain

To switch, set ZEPHYR_TOOLCHAIN_VARIANT.

Further it is possible to run _native POSIX_ samples that require a display
and check the display output via a VNC client. To allow the VNC client to
connect to the docker instance port 5900 needs to be forwarded to the host,
for example:

```
docker run -ti -p 5900:5900 -v <path to zephyr tree>:/workdir docker-zephyr:ubuntu_18.04-zsdk_0.10.3
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



