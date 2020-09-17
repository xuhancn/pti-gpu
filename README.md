# Profiling Tools Interfaces for GPU (PTI for GPU)

## Overview
This repository describes the ways of collecting performance data for Intel(R) Processor Graphics and provides a set of samples that help to start.

## License
Samples for Profiling Tools Interfaces for GPU (PTI for GPU) are distributed under the MIT License.

You may obtain a copy of the License at https://opensource.org/licenses/MIT

## Supported OS
- Linux

*Windows support is under development*

## Supported Platforms
- Intel(R) Processor Graphics Gen9 (formerly Skylake) and newer

*Some samples may have higher hardware requirements*

## Tested Configurations
- Ubuntu 19.10 with Intel(R) Iris(R) Plus Graphics 655

## Profiling Chapters
1. Runtime API Tracing
    - for [OpenCL(TM)](chapters/runtime_api_tracing/OpenCL.md)
    - for [oneAPI Level Zero (Level Zero)](chapters/runtime_api_tracing/LevelZero.md)
    - for [OpenMP*](chapters/runtime_api_tracing/OMPT.md)
2. Device Activity Tracing
    - for [OpenCL(TM)](chapters/device_activity_tracing/OpenCL.md)
    - for [oneAPI Level Zero (Level Zero)](chapters/device_activity_tracing/LevelZero.md)
3. Binary/Source Correlation
    - for [OpenCL(TM)](chapters/binary_source_correlation/OpenCL.md)
    - for [oneAPI Level Zero (Level Zero)](chapters/binary_source_correlation/LevelZero.md)
4. Metrics Collection
    - for [oneAPI Level Zero (Level Zero)](chapters/metrics_collection/LevelZero.md)
    - based on [Intel(R) Metrics Discovery Application Programming Interface](chapters/metrics_collection/MetricsDiscoveryAPI.md)
    - based on [Performance Monitoring (PM) Register](chapters/metrics_collection/PerfMonReg.md)
5. Binary Instrumentation
    - based on [Graphics Technology Pin (GT Pin)](chapters/binary_instrumentation/GTPin.md)
    - based on [OpenCL(TM) built-in intrinsics](chapters/binary_instrumentation/OpenCLBuiltIn.md)
6. Code Annotation
    - based on [Instrumentation and Tracing Technology API (ITT API)](chapters/code_annotation/ITT.md)

## Prerequisites
- [CMake](https://cmake.org/) (version 2.8 and above)
- [Git](https://git-scm.com/) (version 1.8 and above)
- [Python](https://www.python.org/) (version 2.8 and above)
- One have to be a part of `video` user group to do any computations on Intel(R) Processor Graphics:
    ```sh
    sudo usermod -a -G video <username>
    ```
- OpenCL(TM) [ICD Loader](https://github.com/KhronosGroup/OpenCL-ICD-Loader) and [Headers](https://github.com/KhronosGroup/OpenCL-Headers)
    - to use non-standard path to OpenCL ICD library one may add it into `LD_LIBRARY_PATH`:
        ```sh
        export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<path-to-opencl>
        ```
- [oneAPI Level Zero loader](https://github.com/oneapi-src/level-zero)
- [Intel(R) Graphics Compute Runtime for oneAPI Level Zero and OpenCL(TM) Driver](https://github.com/intel/compute-runtime)
- [Intel(R) Metrics Discovery Application Programming Interface](https://github.com/intel/metrics-discovery)
    - one may need to install `libdrm-dev` package to build the library from sources
    - one may need to allow metrics collection for non-root users:
        ```sh
        sudo echo 0 > /proc/sys/dev/i915/perf_stream_paranoid
        ```
- [Metrics Library for Metrics Discovery API (Metrics Library for MD API)](https://github.com/intel/metrics-library)
- [Graphics Technology Pin (GT Pin)](./gtpin)
- [Intel(R) oneAPI HPC Toolkit](https://software.intel.com/en-us/oneapi/hpc-kit)
- [libdrm](https://gitlab.freedesktop.org/mesa/drm)
    - on Ubuntu one may perform:
        ```
        sudo apt-get install libdrm-dev
        ```

More information of what is needed for particular sample can be found on sample description page.

## Build and Run
In general, to build samples one need to perform the following steps (specific instructions for particular sample can be found on sample description page):
```sh
cd <pti_root>/samples/<sample_root>
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

To point out to specific headers and libraries one may use `-DCMAKE_INCLUDE_PATH` and `-DCMAKE_LIBRARY_PATH` options correspondingly, e.g.:
```sh
cmake -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INCLUDE_PATH=/tmp/level_zero/include \
  -DCMAKE_LIBRARY_PATH=/tmp/level_zero/lib \
  ..
```

Run instructions may vary from sample to sample significantly, so they are provided on particular sample description page.

## Testing
There is a way to build and test all the samples in one command, e.g.:
```sh
LD_LIBRARY_PATH=/usr/local/lib python <pti_root>/tests/run.py
```
In case of failed tests, error output will be available in `stderr.log` file.

It's also possible to test an exact sample or a group of samples, e.g.:
```sh
python <pti_root>/tests/run.py -s cl_hot_functions # build and test an exact sample "cl_hot_functions"
python <pti_root>/tests/run.py -s ze # build and test all L0 samples
```
To run tesing in debug mode one may use `-d` option, e.g.:
```sh
python <pti_root>/tests/run.py -s ze_gemm -d
```
The script creates `build` directory inside each sample folder while testing. To remove all of these folders, use:
```sh
python <pti_root>/tests/run.py -c
```

**Tested software versions one may find in [SOFTWARE](SOFTWARE) file.**

## Known Issues
1. On RHEL IGA library may not be found even after [Intel(R) Graphics Compute Runtime for oneAPI Level Zero and OpenCL(TM) Driver](https://github.com/intel/compute-runtime) installation. To fix it, make a link `libiga64.so` to `libiga64.so.1`, e.g.:
    ```sh
    cd /usr/lib64
    sudo ln -s libiga64.so.1 libiga64.so
    cd -
    ```
2. On RHEL one may need to use newer compiler. To enable it, one may fix `PATH` and `LD_LIBRARY_PATH` variables, e.g.:
    ```sh
    export PATH=/opt/gcc/7.4.0/bin/:$PATH
    export LD_LIBRARY_PATH=/opt/gcc/7.4.0/lib:/opt/gcc/7.4.0/lib64:$LD_LIBRARY_PATH
    ```
3. On Ubuntu 19.04 (Linux kernel 5.3) it may be needed to add user into "render" group to run GPU applications:
    ```sh
    sudo usermod -a -G render <username>
    ```

##
**(*) Other names and brands my be claimed as property of others**
