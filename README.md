# Tensorflow 2.1 for Pynq Z2 ( zynq 7000 xilinx SoC )
The wheel has been produced cross compiling with different compiler's flags using the script provided by tensorflow for building it for rasberry since the official binaries does not work on the board.

The compilation chain used is the official one for the Rasberry 

Below the part which i modified for the entire tensorflow ( wrt to tensorflow repository ./tensorflow/tools/ci_build/pi/build_raspberry_pi.sh ) :
```bash
  PI_COPTS="--copt=-march=armv7-a --copt=-mfpu=vfpv3-d16
  --copt=-mfloat-abi=hard 
  --copt=-std=gnu11 --copt=-DS_IREAD=S_IRUSR --copt=-DS_IWRITE=S_IWUSR
  --copt=-O3 --copt=-fno-tree-pre --copt=-fpermissive
  --copt=-U__GCC_HAVE_SYNC_COMPARE_AND_SWAP_1
  --copt=-U__GCC_HAVE_SYNC_COMPARE_AND_SWAP_2
  --copt=-U__GCC_HAVE_SYNC_COMPARE_AND_SWAP_8
  --define=raspberry_pi_with_neon=true"
  WHEEL_ARCH=linux_armv7l
  echo "Building for the Pi Two/Three, with NEON acceleration"
```

if you have a different xilinx board you can just check the cpu's flags with:
```bash
cat /proc/cpuinfo
```
and modify the copt accordingly.

Below the part to modify for tensorflow lite ( wrt to tensorlfow repository ./tensorflow/lite/tools/make/targets/rpi_makefile.rpi):
```bash
 CXXFLAGS += \
                        -march=armv7-a \
      -mfpu=vfpv3-d16 \
      -mfloat-abi=hard \
      -funsafe-math-optimizations \
      -ftree-vectorize \
      -fPIC

    CFLAGS += \
      -march=armv7-a \
      -mfpu=vfpv3-d16 \
      -mfloat-abi=hard \
      -funsafe-math-optimizations \
      -ftree-vectorize \
      -fPIC
```

Then:
```bash
 sudo apt install swig libjpeg-dev zlib1g-dev python3-dev python3-numpy
 ./tensorflow/lite/tools/make/build_rpi_lib.sh
 cd tensorflow/lite/tools/pip_package/
 make BASE_IMAGE=debian:buster PYTHON=python3 TENSORFLOW_TARGET=rpi docker-build # for python3.7
 make BASE_IMAGE=debian:stretch PYTHON=python3 TENSORFLOW_TARGET=rpi docker-build # for python3.5
```


## Set up Host and Build
you can follow the official guide on tensorflow: 
[Build from source for the Raspberry Pi](https://www.tensorflow.org/install/source_rpi#python-3)

## Set up Target 
The wheel has been produced for python3.5, therefore you have to install python3.5 on the target. 
If you need a pynq enviroment in tensorflow you need to install pynq from scratch again, and it has been made available using pip:
```bash
sudo pip3.5 install pynq
```
After you start the installation of pynq, go grab a coffe and do laundry. It takes a while.

## Install 
For installing the wheel, just copy it on the sd card and in the terminal write:
```bash
sudo pip.5 install ./tensorflow-2.1.0-cp35-none-linux_armv7l.whl
```
It will also download and install all the dependancies, some of them have to be compiled on the field (such as scipy and h5py). It will take a while, go out and get drunk, hopefully when you will be back it should have finished.
And you could create a drunk neural network :)


