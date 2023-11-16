---
abbrlink: ''
categories:
  - 学习
  - 代码知识
date: '2023-11-16T15:40:51.781823+08:00'
description: 简便计算FID,ISC,KID,PPL
tags:
  - pytorch
title: '简便计算FID,ISC,KID,PPL：torch-fidelity '
updated: 2023-11-16T15:40:51.935+8:0
---
# 地址信息

项目地址:
[https://github.com/toshas/torch-fidelity](https://github.com/toshas/torch-fidelity)
文档地址:
[https://torch-fidelity.readthedocs.io/en/latest/usage_cmd.html](https://torch-fidelity.readthedocs.io/en/latest/usage_cmd.html)

# 使用方法

譬如我需要计算两个数据集的FID

1. `pip install torch-fidelity`
2. fid计算：`fidelity  --input1 F:/dataset2/ --input2 F:/dataset1/ -g 0 -f`

# 备注

可能会报错
`Initializing libiomp5md.dll, but found libiomp5md.dll already initialized.`
去你的torch文件夹里把`Initializing libiomp5md.dll`删了就可以。

