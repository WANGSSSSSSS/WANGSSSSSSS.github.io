---
title: opencv和cuda
date: 2021-03-27 16:31:25
categories:
- environment setup
tags:
- opencv
- cuda
---

## opencv4.5 和 cuda一起编译

opencv现在越写越大，原来简单的cmake配置现在变得烦的一批，为了下一次别人问我的时候，我不尴尬，我决定这次把编译方案记录下来，也好流芳百世（雾）.

* cmake and git

  ```bash
  sudo apt install cmake
  sudo apt install git
  ```

* opencv  download

  ```shell
  git clone https://github.com/opencv/opencv.git
  git clone https://github.com/opencv/opencv_contrib.git
  ```

* cuda 11

  此处建议直接去官网下载，并不会浪费很多时间，下载速度也很快

  ```shell
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
  sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600wget https://developer.download.nvidia.com/compute/cuda/11.2.2/local_installers/cuda-repo-ubuntu2004-11-2-local_11.2.2-460.32.03-1_amd64.deb
  sudo dpkg -i cuda-repo-ubuntu2004-11-2-local_11.2.2-460.32.03-1_amd64.deb
  sudo apt-key add /var/cuda-repo-ubuntu2004-11-2-local/7fa2af80.pub
  sudo apt-get update
  sudo apt-get -y install cuda
  ```

* cmake 编译设置

  ```shell
  cmake -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules
        -DWITH_CUDA=ON 
        -DCUDA_FAST_MATH=ON
        -DWITH_CUBLAS=ON  
        -DCUDA_ARCH_PTX=7.5 
        -DCUDA_ARCH_BIN=7.5 
        ..
  ```

  解释一下：

  英明的英伟达对显卡计算能力做了分级，可以自己搜一下就明白那个算力数字的含义，其实可以感觉到这是架构不同或者说硬件结构的限制，因此需要区别版本，如果不加那两句进行编译，也是可以的，这样表示默认，全部cuda支持硬件架构你都要编译，时间上会花费很久。

