---
title: React-Native-issues
date: 2019-10-11 17:24:32
tags:
---
1. 问题：use 调试时，某些手机安装 apk 之后，手机自带卸载该 apk，通过命令行重新安装会报告：`Activity class {com.reactnativedemo/com.reactnativedemo.MainActivity} does not exist.`;
>解决办法: 命令行执行 adb uninstall {apk 包名}，如`adb uninstall reactnativedemo`，然后即可重新安装运行