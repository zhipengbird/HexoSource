---
title: GPUImage 学习笔记
date: 2016-10-23 22:04:14
categories: GPUImage
tags:
- iOS-GPUImage
---
##  GPUImage介绍

* GPUImage 是一个基于 GPU 图像和视频处理的开源 iOS 框架。由于使用 GPU 来处理图像和视频，所以速度非常快. 除了速度上的优势，GPUImage 还提供了很多很棒的图像处理滤镜，但有时候这些基本功能仍然无法满足实际开发中的需求，GPUImage 还支持自定义滤镜.
* GPUImage 可使用cocoapods集成到项目中.
  ```sh
  pod 'GPUImage', '~> 0.1.7'
  ```
* 如果需要对同一张图片添加多个不同的滤镜, 可以一个一个的 添加.
