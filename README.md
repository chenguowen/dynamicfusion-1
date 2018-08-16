# DynamicFusion 

## 第一个问题和第二个问题都被证明是输入数据的错误， Kinect数据是近处的黑色远处是白色的吗？  
testtest@hhh:~/DynamicFusion2-mihaibujanca-underdevelopment/build$ ./bin/dynamicfusion ../Depth/
dir_name=../Depth/
depths.size()=15
depths.size()=15
KinFu2 error: invalid pitch argument    /home/testtest/DynamicFusion2-mihaibujanca-underdevelopment/kfusion/src/device_memory.cpp:231

## 第三个问题是由OPT引起的，开发环境是ubuntu16.04,1080TI,cuda8.0, sm 6.1 ：

[string "<string>"]:137: cuInit: cuda reported error -1
stack traceback:
        [C]: in function 'error'
        [string "<string>"]:137: in function 'localversion'
        [string "<string>"]:236: in function 'cudacompile'
        ...DynamicFusion2-M/dynamicfusion-1/deps/Opt/API/src/util.t:874: in function 'makeGPUFunctions'
        .../dynamicfusion-1/deps/Opt/API/src/solverGPUGaussNewton.t:751: in function 'compilePlan'
        ...st/DynamicFusion2-M/dynamicfusion-1/deps/Opt/API/src/o.t:870: in function <...st/DynamicFusion2-M/dynamicfusion-1/deps/Opt/API/src/o.t:862>
        [C]: in function 'xpcall'
        ...st/DynamicFusion2-M/dynamicfusion-1/deps/Opt/API/src/o.t:862: in function <...st/DynamicFusion2-M/dynamicfusion-1/deps/Opt/API/src/o.t:861>
dynamicfusion: /home/testtest/DynamicFusion2-M/dynamicfusion-1/deps/Opt/examples/shared/OptSolver.h:56: OptSolver::OptSolver(const std::vector<unsigned int>&, const string&, const string&, bool): Assertion `m_plan' failed.
Aborted (core dumped)


目前old和new服务器都测试了 Opt的sanmple跑不通，terra最新版git下载的自己编译不通过。 
所以dynamic fusion都不行。 
但是在docker环境中，一个自带例子的可以跑到marchingCube，单独环境的docker出错vtk，说没法渲染/ 


旧服务器安装7.5失败 。 




============
Implementation of [Newcombe et al. 2015 DynamicFusion paper](http://grail.cs.washington.edu/projects/dynamicfusion/papers/DynamicFusion.pdf).

#### This project is still in active development and does not yet reproduce the results of the paper accurately.

The code is based on this [KinectFusion implemenation](https://github.com/Nerei/kinfu_remake)

## Building instructions:

### Ubuntu 16.04
Clone dynamicfusion and dependencies. 
```
git clone https://github.com/mihaibujanca/dynamicfusion --recursive
```

Install NVIDIA drivers.
- Enable NVidia drivers (Search / Additional Drivers) selecting:
	"Using NVIDIA binary driver - version 375.66 from nvidia-375 (proprietary, tested)"
	"Using processor microcode firmware for Intel CPUs from intel-microcode (proprietary)"
- Restart pc to complete installation

Alternatively a good tutorial with some common issues covered can be found [here](
              https://askubuntu.com/a/61433/167689).

For fresh installs (this assumes you cloned your project in your home directory!):
```
chmod +x build.sh
./build.sh
```

If you are not on a fresh install, check `build.sh` for building instructions and dependencies.

If you want to build the tests as well, set `-DBUILD_TESTS=ON`.\
To save frames showing the reconstruction progress, pass `-DSAVE_RECONSTRUCTION_FRAMES=ON`. The frames will be saved in `<project_root>/output`

To build documentation, go to the project root directory and execute
```
doxygen -g
doxygen Doxyfile
```


### Running
```
./download_data 
./build/bin/dynamicfusion data/umbrella
```

### Windows
Dependencies:
* CUDA 5.0 or higher
* OpenCV 2.4.8 or higher (modules opencv_core, opencv_highgui, opencv_calib3d, opencv_imgproc, opencv_viz). Make sure that WITH_VTK flag is enabled in CMake during OpenCV configuration.
* Boost (libraries system, filesystem and program options. Only used in the demo. Tested with [1.64.0](http://www.boost.org/users/history/version_1_64_0.html))
* Ceres solver (Tested with version [1.13.0](http://ceres-solver.org/ceres-solver-1.13.0.tar.gz))

Implicit dependency (needed by opencv_viz):
* VTK 5.8.0 or higher
* SuiteSparse, BLAS and LAPACK for ceres
Optional dependencies:
* GTest for testing
* Doxygen for documentation
* OpenNI v1.5.4 for getting input straight from a kinect device.

[Install NVIDIA drivers](https://www.geforce.com/drivers) and [CUDA](https://developer.nvidia.com/cuda-downloads)
* [Install LAPACK](http://icl.cs.utk.edu/lapack-for-windows/lapack/).
* [Install VTK](http://www.vtk.org/download/) (download and build from source)
* [Install OpenCV](http://docs.opencv.org/3.2.0/d3/d52/tutorial_windows_install.html).  
* [Install Boost](http://www.boost.org/users/download/)

 
Optionals:
* [Doxygen](http://www.stack.nl/~dimitri/doxygen/download.html)
* [GTest](https://github.com/google/googletest) 
* [OpenNI]( http://pointclouds.org/downloads/windows.html)

[Download the dataset](http://lgdv.cs.fau.de/uploads/publications/data/innmann2016deform/umbrella_data.zip).\
Create a `data` folder inside the project root directory. \
Unzip the archive into `data` and remove any files that are not .png. \
Inside `data`, create directories `color` and `depth`, and move color and depth frames to their corresponding folders.

To use with .oni captures or straight from a kinect device, use `./build/bin/dynamicfusion_kinect <path-to-oni>` or `./build/bin/dynamicfusion_kinect <device_id>` 

---
Note: currently, the frame rate is too low (10s / frame) to be able to cope with live inputs, so it is advisable that you capture your input first.

## References
[DynamicFusion project page](http://grail.cs.washington.edu/projects/dynamicfusion/)

```
@InProceedings{Newcombe_2015_CVPR,
author = {Newcombe, Richard A. and Fox, Dieter and Seitz, Steven M.},
title = {DynamicFusion: Reconstruction and Tracking of Non-Rigid Scenes in Real-Time},
booktitle = {The IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
month = {June},
year = {2015}
}
```

The example dataset is taken from the [VolumeDeform project](http://lgdv.cs.fau.de/publications/publication/Pub.2016.tech.IMMD.IMMD9.volume_6/).
```
@inbook{innmann2016volume,
author = "Innmann, Matthias and Zollh{\"o}fer, Michael and Nie{\ss}ner, Matthias and Theobalt, Christian 
         and Stamminger, Marc",
editor = "Leibe, Bastian and Matas, Jiri and Sebe, Nicu and Welling, Max",
title = "VolumeDeform: Real-Time Volumetric Non-rigid Reconstruction",
bookTitle = "Computer Vision -- ECCV 2016: 14th European Conference, Amsterdam, The Netherlands,
            October 11-14, 2016, Proceedings, Part VIII",
year = "2016",
publisher = "Springer International Publishing",
address = "Cham",
pages = "362--379",
isbn = "978-3-319-46484-8",
doi = "10.1007/978-3-319-46484-8_22",
url = "http://dx.doi.org/10.1007/978-3-319-46484-8_22"
}
```
