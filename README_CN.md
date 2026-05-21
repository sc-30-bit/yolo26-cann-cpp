# yolo26-cann-cpp

[English](./README.md)

<p align="left">
  <img alt="platform" src="https://img.shields.io/badge/Platform-Huawei%20Atlas-blue">
  <img alt="toolkit" src="https://img.shields.io/badge/Toolkit-CANN-orange">
  <img alt="language" src="https://img.shields.io/badge/C%2B%2B-17-brightgreen">
  <img alt="opencv" src="https://img.shields.io/badge/OpenCV-required-yellowgreen">
</p>

## 项目简介

本仓库提供 **YOLO26** 在 **华为 Atlas** 设备上的 C++ 部署示例，基于 **CANN / AscendCL** 实现，并提供可直接下载的 [CANN `.om` 权重文件](https://drive.google.com/file/d/1saF-3-yTGZoNJAgP1Gg8CqcY1rL6p_rT/view?usp=sharing)，方便快速运行。当前支持 **FP16 推理**、**AIPP 预处理**、**纯检测**、**检测 + 跟踪**、**视频文件推理** 和 **USB 摄像头推理**。

## 效果示例

| 检测 | 跟踪 |
| --- | --- |
| <img src="./figs/detection.jpg" alt="YOLO26 检测示例" width="420"> | <img src="./figs/tracking.jpg" alt="YOLO26 跟踪示例" width="420"> |

## 仓库结构

```text
yolo26-cann-cpp/
|-- CANN-CPP-DETECTION/
|   |-- CMakeLists.txt
|   `-- main.cpp
|-- CANN-CPP-TRACK/
|   |-- CMakeLists.txt
|   `-- main.cpp
|-- figs/
|   |-- detection.jpg
|   `-- tracking.jpg
|-- .gitignore
|-- README.md
`-- README_CN.md
```

## 环境要求

需要一台已安装 CANN Toolkit 的华为 Atlas 设备，并准备好 OpenCV、CMake 和 GCC/G++。

## 模型权重

CANN `.om` 模型可从 [Google Drive](https://drive.google.com/file/d/1saF-3-yTGZoNJAgP1Gg8CqcY1rL6p_rT/view?usp=sharing) 下载。

两个示例默认读取的模型路径为：

```text
/home/HwHiAiUser/YOLO26/yolo26nfp16_aipp_opt3.om
```

下载后将模型复制到该路径即可运行；如果模型放在其他位置，请修改 `CANN-CPP-DETECTION/main.cpp` 和 `CANN-CPP-TRACK/main.cpp` 中的 `kModelPath`。

## 编译

### 检测示例

```bash
cd CANN-CPP-DETECTION
mkdir -p build
cd build
cmake ..
make -j4
```

### 跟踪示例

```bash
cd CANN-CPP-TRACK
mkdir -p build
cd build
cmake ..
make -j4
```

## 快速运行

### 1. 视频文件推理

在 `main.cpp` 中设置：

```cpp
const std::string kVideoSource = "/path/to/your/video.mp4";
```

然后运行：

```bash
./yolo26_acl_demo
```

### 2. USB 摄像头推理

在 `main.cpp` 中设置：

```cpp
const std::string kVideoSource = "0";
```

这会打开默认摄像头，通常对应 `/dev/video0`。如需调整摄像头分辨率，可在 OpenCV 的 `cap.open()` 之后设置相关参数。

## AIPP 说明

当前部署流程使用静态 AIPP 预处理，常用配置如下：

```text
aipp_op {
    aipp_mode: static
    input_format: RGB888_U8
    csc_switch: false
    src_image_size_w: 640
    src_image_size_h: 640

    mean_chn_0: 0
    mean_chn_1: 0
    mean_chn_2: 0
    var_reci_chn_0: 0.003921568627
    var_reci_chn_1: 0.003921568627
    var_reci_chn_2: 0.003921568627
}
```

该配置会将 RGB 输入从 `[0, 255]` 映射到 `[0, 1]`。

## CMake 说明

项目使用系统中的 OpenCV，并从本机 CANN Toolkit 安装目录中查找 Ascend 头文件和 `libascendcl.so`。
