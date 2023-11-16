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
updated: 2023-11-16T16:9:26.825+8:0
---
# 地址信息

项目地址:
[https://github.com/toshas/torch-fidelity](https://github.com/toshas/torch-fidelity)
文档地址:
[https://torch-fidelity.readthedocs.io/en/latest/usage_cmd.html](https://torch-fidelity.readthedocs.io/en/latest/usage_cmd.html)

# 使用方法

譬如我需要计算两个数据集的FID

1. `pip install torch-fidelity`
2. 计算IS:`fidelity --gpu 0 --isc --input1 F:/GlasgowV6/genimg_mixlabel/1/`
3. 计算ISC:`fidelity --gpu 0 --isc --input1 F:/GlasgowV6/genimg_mixlabel/1/ --input2 F:/GlasgowV6/datapic/01/`
4. 计算FID:`fidelity --gpu 0 --fid --input1 F:/GlasgowV6/genimg_mixlabel/1/ --input2 F:/GlasgowV6/datapic/01/`
5. 计算KID:`fidelity --gpu 0 --kid --input1 F:/GlasgowV6/genimg_mixlabel/1/ --input2 F:/GlasgowV6/datapic/01/ --kid-subset-size 230`

# 备注

可能会报错
`Initializing libiomp5md.dll, but found libiomp5md.dll already initialized.`
去你的torch文件夹里把`Initializing libiomp5md.dll`删了就可以。

计算KID需要两个文件夹下样本数量相同，加个参数即可，例如`--kid-subset-size 230`
