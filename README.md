# SFND 2D Feature Tracking

<img src="images/keypoints.png" width="820" height="248" />

The idea of the camera course is to build a collision detection system - that's the overall goal for the Final Project. As a preparation for this, you will now build the feature tracking part and test various detector / descriptor combinations to see which ones perform best. This mid-term project consists of four parts:

* First, you will focus on loading images, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, you will integrate several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and compare them with regard to number of keypoints and speed. 
* In the next part, you will then focus on descriptor extraction and matching using brute force and also the FLANN approach we discussed in the previous lesson. 
* In the last part, once the code framework is complete, you will test the various algorithms in different combinations and compare them with regard to some performance measures. 

See the classroom instruction and code comments for more details on each of these parts. Once you are finished with this project, the keypoint matching part will be set up and you can proceed to the next lesson, where the focus is on integrating Lidar points and on object detection using deep-learning. 

## Dependencies for Running Locally
1. cmake >= 2.8
 * All OSes: [click here for installation instructions](https://cmake.org/install/)

2. make >= 4.1 (Linux, Mac), 3.81 (Windows)
 * Linux: make is installed by default on most Linux distros
 * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
 * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)

3. OpenCV >= 4.1
 * All OSes: refer to the [official instructions](https://docs.opencv.org/master/df/d65/tutorial_table_of_content_introduction.html)
 * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors. If using [homebrew](https://brew.sh/): `$> brew install --build-from-source opencv` will install required dependencies and compile opencv with the `opencv_contrib` module by default (no need to set `-DOPENCV_ENABLE_NONFREE=ON` manually). 
 * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)

4. gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using either [MinGW-w64](http://mingw-w64.org/doku.php/start) or [Microsoft's VCPKG, a C++ package manager](https://docs.microsoft.com/en-us/cpp/build/install-vcpkg?view=msvc-160&tabs=windows). VCPKG maintains its own binary distributions of OpenCV and many other packages. To see what packages are available, type `vcpkg search` at the command prompt. For example, once you've _VCPKG_ installed, you can install _OpenCV 4.1_ with the command:
```bash
c:\vcpkg> vcpkg install opencv4[nonfree,contrib]:x64-windows
```
Then, add *C:\vcpkg\installed\x64-windows\bin* and *C:\vcpkg\installed\x64-windows\debug\bin* to your user's _PATH_ variable. Also, set the _CMake Toolchain File_ to *c:\vcpkg\scripts\buildsystems\vcpkg.cmake*.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.

## Task 9. Performance evaluation 3

### Keypoints Data 
First we need to make some analysis of the extracted detector data:

| Detector  | avg keypoints all |  avg keypoints ROI |  avg time (ms) | 
|-----------|-------------------|--------------------|----------------|
| AKAZE     |        1343       |        167         |      82.14     |
| SIFT      |        1386       |        139         |     113.59     |
| SHITOMASI |        1342       |        118         |      21.25     |
| HARRIS    |         174       |         25         |      23.45     |
| FAST      |        4921       |        410         |       4.35     |
| BRISK     |        2712       |        276         |     277.56     |
| ORB       |         500       |        116         |      14.04     |

### Descriptor-detector data 

Based on that and the data obtained by combining different detector-descriptor pairs we can get the following data:

| Detector\Descriptor  | BRISK | BRIEF | ORB  | FREAK | AKAZE | SIFT  | 
|----------------------|-------|-------|------|-------|-------|-------|
| BRISK                | 2.85  | 1.00  | 4.53 | 40.01 | N/A   | 44.69 |
| ORB                  | 1.37  | 0.97  | 6.30 | 38.93 | N/A   | 51.65 |
| AKAZE                | 2.10  | 0.84  | 2.72 | 40.76 | 67.41 | 26.60 |
| SIFT                 | 1.54  | 0.63  | N/A  | 38.53 | N/A   | 90.32 |
| SHITOMASI            | 1.45  | 1.04  | 1.25 | 37.34 | N/A   | 19.79 |
| HARRIS               | 0.52  | 0.59  | 1.01 | 36.99 | N/A   | 19.58 |
| FAST                 | 4.16  | 2.69  | 2.53 | 45.29 | N/A   | 42.11 |

Finally getting the average number of points matched for the same descriptor-detector pairs

| Detector\Descriptor  | BRISK | BRIEF | ORB  | FREAK | AKAZE | SIFT  | 
|----------------------|-------|-------|------|-------|-------|-------|
| BRISK                | 175   | 190   | 169  | 170   | N/A   | 183   |
| ORB                  | 84    | 61    | 85   | 47    | N/A   | 85    |
| AKAZE                | 135   | 141   | 132  | 132   | 140   | 141   |
| SIFT                 | 66    | 78    | N/A  | 66    | N/A   | 89    |
| SHITOMASI            | 85    | 105   | 101  | 85    | N/A   | 103   |
| HARRIS               | 16    | 20    | 18   | 16    | N/A   | 18    |
| FAST                 | 243   | 315   | 308  | 248   | N/A   | 310   |

### Final results and best performers

From the data above we can conclude that the FAST detector can retrieve the most points per image taking less time than the rest of descriptors, the top 3 performer pairs are being shown next: 

1. FAST-BRIED
2. FAST-ORB
3. FAST-BRISK

The FAST detector and BRIEF descriptor show the most promising results and is the recommended approach.