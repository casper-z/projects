## [Face Recognition Analyzer](./README.md) - 人脸视频分析器项目
项目介绍 | [部署流程](./document/README-deploy.md) | [接口文档](./document/README-api.md)

#### 项目介绍
基于NVIDIA DeepStream + InsightFace 实现的人脸识别项目。

#### 应用场景
* 人脸考勤

#### 功能说明
* 内置了NVIDIA DeepStream 6.0
* 内置了NVIDIA NvDCF目标跟踪算法
* 内置了NVIDIA facenet人脸检测算法 2.0.1
* 集成了InsightFace scrfd人脸检测算法 500m
* 集成了InsightFace arcface人脸识别算法 r100
* 支持手动上传人脸头像
* 支持批量标注人脸头像
* 支持800w摄像机

#### 运行环境
* 硬件 Jetson nx
* 系统 Ubuntu 18.04
* 驱动 Jetson Package 4.6
