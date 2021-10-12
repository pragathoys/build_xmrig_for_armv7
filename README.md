# How to build the XMRig for a Raspberry Pi machine with ARMv7 architecture
In the following notes we assume that you want to build the cpuminer-multi software for a Raspberry Pi with ARMv7 architecture.

Basically, ARMv7 and below are 32-bit while ARMv8 introduces the 64-bit instruction set. The latest version of Raspberry Pi 4 comes with an ARMv8 64-bit instruction set.

We will use the release v5.11.4 from the xmrig repository (but you can even compile v6.2.3).

# Check your machine

Just to make sure you have an armv7 RPi you can run the following command in a shell prompt:

```shell
root@raspberrypi:~# uname -m
```

You should get a response like this:

```shell
root@raspberrypi:~# uname -m
armv7l
root@raspberrypi:~# 
```

Another way to check the architecture of your CPU is the lscpu command:
```shell
root@raspberrypi:~# lscpu 
Architecture:        armv7l
Byte Order:          Little Endian
CPU(s):              4
On-line CPU(s) list: 0-3
Thread(s) per core:  1
Core(s) per socket:  4
Socket(s):           1
Vendor ID:           ARM
Model:               4
Model name:          Cortex-A53
Stepping:            r0p4
CPU max MHz:         1200.0000
CPU min MHz:         600.0000
BogoMIPS:            38.40
Flags:               half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae evtstrm crc32
```

# Install the required libraries in the system

Before starting the build please make sure you have installed the following libraries (make sure you are logged in as root otherwise you will have to use sudo infront of each of the commands):

```shell
root@raspberrypi:~#  apt update
root@raspberrypi:~#  apt-get install git build-essential cmake libuv1-dev libssl-dev libhwloc-dev
```

# Clone the GitHub repository of the software
We will use the main repository :

```shell
root@raspberrypi:~# wget https://github.com/xmrig/xmrig/archive/refs/tags/v5.11.4.zip
root@raspberrypi:~# unzip v5.11.4.zip
```

# Prepare and build
```shell
root@raspberrypi:~# cd xmrig-5.11.4
root@raspberrypi:~/xmrig-5.11.4# mkdir build
root@raspberrypi:~/xmrig-5.11.4# cd build
root@raspberrypi:~/xmrig-5.11.4/build# cmake .. -DARM_TARGET=7
root@raspberrypi:~/xmrig-5.11.4/build# make
```
If you get any errors about
```shell
undefined reference to `__atomic...
```
please add following line to the file CMakeLists.txt:
open with an editor:
```shell
nano CMakeLists.txt
```
and under the line:
```shell
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
```
please add also this line:
```shell
set(CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS} -latomic")
```

Rerun the cmake command and if no errors appear you are ready to go:

```shell
root@raspberrypi:~/xmrig-5.11.4/build# make
```

# Check miner

If everything gone well when you run the command :

```shell
root@raspberrypi:~/xmrig-5.11.4/build# ./xmrig --version 
```

You should get a similar screen like this:

```shell
XMRig 5.11.4
 built on Oct 12 2021 with GCC 8.3.0
 features:

libuv/1.24.1
OpenSSL/1.1.1d
hwloc/1
```

#  Start mining!

You are ready to create the configuration file,use your favorite mining pool and start mining:

```shell
root@raspberrypi:~/xmrig-5.11.4/build#  ./xmrig -c <config_filename>
```

# Resources

* Most usefull resource is the repository of the xmrig: https://github.com/xmrig/xmrig.git
* Release v5.11.4: https://github.com/xmrig/xmrig/releases/tag/v5.11.4
* Build instructions: https://github.com/xmrig/xmrig/wiki/Ubuntu-Build
* https://github.com/xmrig/xmrig/issues/744
* https://github.com/xmrig/xmrig/issues/1224
* https://github.com/xmrig/xmrig/blob/master/src/config.json
* More details about the various architectures for RPi : https://en.wikipedia.org/wiki/Raspberry_Pi
