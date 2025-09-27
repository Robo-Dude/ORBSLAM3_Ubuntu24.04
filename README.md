# ORBSLAM3_Ubuntu24.04

# ORB-SLAM3 for Ubuntu 24.04 (OpenCV 4.12 + CUDA 12.9)

A ready-to-build **ORB-SLAM3** fork updated for **Ubuntu 24.04** with:
- **OpenCV 4.12** (CUDA-enabled)
- **CUDA 12.9** (with cuDNN)
- Compatible with **modern CMake & GCC** on Ubuntu 24.04
- Generates the shared library `libORB_SLAM3.so` for use in wrappers or custom apps.

This repository provides:
- Updated **ORB-SLAM3 source** patched for Ubuntu 24.04 / OpenCV 4.12.
- Step-by-step build guide.
- Example instructions to run the **native ORB-SLAM3 demos** (no ROS 2).

> ⚠️ **Note:** This repo is **only for ORB-SLAM3 core**.  
> ROS 2 wrappers and TF/odometry publishers are kept in a separate repository.

---

## 1. Environment

| Component        | Version Tested |
|------------------|----------------|
| OS               | Ubuntu 24.04 LTS |
| OpenCV           | 4.12 (built from source with CUDA 12.9 + cuDNN 9) |
| CUDA             | 12.9 |
| cuDNN            | 9.x |
| CMake            | ≥ 3.22 |
| GCC / G++        | ≥ 11 |
| Pangolin         | Latest master |
| Eigen            | 3.x |
| SuiteSparse      | ≥ 5 |

> 📝 A separate guide for **OpenCV 4.12 + CUDA 12.9** setup is available [here](https://github.com/Robo-Dude/Cuda_opencv_4.12).

---

---

## 2. Dependencies Setup (Choose Your OpenCV Version)

Before building ORB-SLAM3, install these core libraries **in order**:

1. **Eigen3**
2. **Pangolin** (for the ORB-SLAM3 Viewer)
3. **OpenCV** → **You have two options here** 👇
   You can either keep the default OpenCV (4.6) or upgrade to 4.12 with CUDA support.

    🔹 Option A: Use Default OpenCV (4.6 – easier)
    
      Ubuntu 24.04 ships with OpenCV 4.6 pre-installed via ROS 2 cv_bridge.
      If you don’t need GPU acceleration, you can skip this step and use the default.
      
      Check installed version:
      ``` 
      pkg-config --modversion opencv4
      # Expected: 4.6.x
      ```

    🔹 Option B: Install OpenCV 4.12 + CUDA 12.9 + cuDNN (for GPU acceleration)

      If you want CUDA acceleration for feature extraction and faster SLAM:
      
      👉 Follow the full step-by-step guide here:
      [OpenCV 4.12 + CUDA 12.9 Setup on Ubuntu 24.04](https://github.com/Robo-Dude/Cuda_opencv_4.12)
      
      ⚠️ Note:
      If you choose OpenCV 4.12, you’ll also need to rebuild ROS 2 cv_bridge and image transport against it later (instructions in the ROS2 ORBSLAM3 Odometry repo).
      For now, ORB-SLAM3 will just use OpenCV directly.

---

### 2.1 Install Eigen3

```bash
sudo apt update
sudo apt install -y libeigen3-dev
```

## 2.2 Install Pangolin (Viewer)

Pangolin is required by ORB-SLAM3 to display the SLAM viewer window.

### Install Dependencies

```bash
sudo apt update
sudo apt install -y \
    cmake \
    build-essential \
    libglew-dev \
    libboost-all-dev \
    libeigen3-dev \
    libpython3-dev \
    python3-numpy \
    ffmpeg \
    libavcodec-dev \
    libavutil-dev \
    libavformat-dev \
    libswscale-dev \
    libdc1394-22-dev \
    libraw1394-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libx11-dev \
    libxrandr-dev \
    libxi-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev
```
### Build Pangolin

  ```bash
  cd ~
  git clone https://github.com/stevenlovegrove/Pangolin.git
  cd Pangolin
  git checkout v0.9.2  # Tested version
  
  # Build
  mkdir build && cd build
  cmake ..
  make -j$(nproc)
  
  # (Optional) Install system-wide
  sudo make install

  ```

---

## 3.  Compile ORB-SLAM3

```bash
cd ~
git clone https://github.com/Robo-Dude/ORBSLAM3_Ubuntu24.04.git ORB_SLAM3
```
> This repository is a modified version of [ORB_SLAM3](https://github.com/zang09/ORB-SLAM3-STEREO-FIXED.git)

#### ⚠️ Configure OpenCV in CMake
  OpenCV 4.12 (recommended):
  No changes required. Proceed to build.
  
  OpenCV 4.6:
  Edit two CMakeLists.txt files to point to your OpenCV 4.6 installation:

  > $ORB_SLAM3_ROOT_PATH/ORB-SLAM3/CMakeLists.txt

  > $ORB_SLAM3_ROOT_PATH/ORB-SLAM3/ThirdParty/DBoW2/CMakeLists.txt

  ```bash
   find_package(OpenCV 4.12) ---> find_package(OpenCV 4.6)
  ```
At Last :

```bash
cd ~
cd ORB-SLAM3
chmod +x build.sh

./build.sh
```
> This will create libORB_SLAM3.so at lib folder and the executables in Examples folder.

#### Important for Ros2 Orb-slam 3 package setup -

```bash
cd ~/ORB-SLAM3/ThirdParty/Sophus/build
sudo make install
```

## 4. Test ORB-SLAM3

 ```bash
cd ~
wget -c http://robotics.ethz.ch/~asl-datasets/ijrr_euroc_mav_dataset/machine_hall/MH_01_easy/MH_01_easy.zip
unzip MH_01_easy.zip -d MH01/
cd ~ORB_SLAM3
./Examples/Monocular/mono_euroc ./Vocabulary/ORBvoc.txt ./Examples/Monocular/EuRoC.yaml ~/MH01 ./Examples/Monocular/EuRoC_TimeStamps/MH01.txt dataset-MH01_mono
```
> Note - unzip the /Vocabulary/ORBvoc.txt

## ROS2 Integration

For using ORB-SLAM3 in a ROS2 environment to publish **odometry and camera TF**, check out the ROS2 wrapper repository:  
[My ORB-SLAM3 ROS2 Wrapper](https://github.com/Robo-Dude/ROS2_ORBSLAM3_Odometry)

> **Note:** This repository only contains the ORB-SLAM3 core setup. The ROS2 integration is provided separately.

---

## License & Credit

This project is based on the original ORB-SLAM3 repository:  
[https://github.com/UZ-SLAMLab/ORB_SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3)  

Modifications were made for **Ubuntu 24.04**, **OpenCV 4.12 with CUDA**, and easier integration with ROS2 wrappers.  

All rights to the original ORB-SLAM3 code belong to **Carlos Campos, Richard Elvira, Juan J. Gómez, José M.M. Montiel, and Juan D. Tardós, University of Zaragoza**.

