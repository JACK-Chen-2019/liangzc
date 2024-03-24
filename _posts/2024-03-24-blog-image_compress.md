---
title: '[论文阅读]CVPR24: Towards Backward-Compatible Continual Learning of Image Compression'
date: 2024-3-24
permalink: /posts/2024/03/image_compress/
tags:
  - Continual Learning
---
[PDF](https://arxiv.org/pdf/2402.18862.pdf), [CODE](https://gitlab.com/viper-purdue/continual-compression)

======

------

## Towards Backward-Compatible Continual Learning of Image Compression, CVPR2024, [PDF](https://arxiv.org/pdf/2402.18862.pdf), [CODE](https://gitlab.com/viper-purdue/continual-compression)

### 图像压缩

- [https://zhuanlan.zhihu.com/p/662233097](https://zhuanlan.zhihu.com/p/662233097)
- **熵编码** ：根据熵的原理实现**无损编码**。

  - 举例：三位编码八个取值 x：a:000 b:001 c:010 d:011 e:100 f:101 g:110 h:111
    假定 x 的值均匀分布，x 的熵为 $H_(x)=8*(-\frac{1}{8}*log_2(\frac{1}{8}))=3
    $
    假定 x 的的概率分布为$\frac{1}{2},\frac{1}{4},\frac{1}{8},\frac{1}{16},\frac{1}{64},\frac{1}{64},\frac{1}{64},\frac{1}{64}$，x 的熵为 $H_(x)=2
    $，目的是让出现概率更大的值的编码位数更小。可以用平均长度为 2 的哈夫曼编码表示：
    a:0, b:10, c:110, d: 1110, e:111100, f:111101, g: 111110, h: 111111
    上述编码的平均编码长度为 2。
- **熵模型**

  - [Variational Image Compression With A Scale Hyperprior](https://arxiv.org/abs/1802.01436)，[参考 blog1](https://blog.csdn.net/hahalidaxin/article/details/117631525)，[参考 blog2](https://zhuanlan.zhihu.com/p/514477269)
  - 需要利用熵模型估计真实的特征分布，基于香农定理，使用估计所得熵模型对隐层表示建模理论上的编码下界为：
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/Oxk3bCeJfoopI9xZwTQclrAInjg.png)
    写成期望的形式：
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/Lv0qbAqVToT3UGxrwOGcAe5WnSc.png)
    当熵模型估计与实际分布完全相同的时候会有编码长度最小。
  - 关键在于引入边信息，捕捉隐层表示的隐藏信息以辅助熵模型的参数生成，从而改善熵模型估计与隐层实际分布不匹配问题。将边信息导入比特流，这使得 decoder 也可以共享熵模型。解压时 decoder 先解压边信息，构建熵模型，之后基于正确的熵模型解压隐层信息。
  - **优化先验概率 q 和真实的边缘概率 p 的 KL 散度等价于优化码率和失真。**
- **图像压缩**

  1. 编码器$f_{enc}$，解码器 $f_{dec
     }$，隐变量 $Z$，利用熵模型建模隐变量的分布 $p_Z(Z)$，
  2. 损失：压缩率（Rate）+ 失真率（Distortion）
     ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/QRDhbA1PnoeEldxEA3icgiYinvf.png)
     $\lambda$用来平衡压缩率和失真率，改变$\lambda$可以实现可变速率压缩。
  3. 可变速率压缩
     从 $p_{\Lambda}$采样多个$\lambda$
     ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/HpDFbn3kiol7GMxQEETcAHPPnMd.png)
  4. pipline
     ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/K7oubcC2Voett4x1akZcll6Jn2e.png)
     特征离散化-》熵模型-》bitstream-》熵模型-》重建

### Continual Image Compression

主要出现的问题有：

- 直接 finetuning pretrained model 会破坏模型的向后适应性，Decoder 不能重建先前模型生成的 bitstream。

![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/QacJbgHMvorH8wxhWEKcGbRVnQh.png)

- 与传统的连续学习相比，通常是的措施是 end-to-end。而图像压缩是两个独立的部分（编码和解码），并且经过了离散化、熵编码（entropy model），另外，最不同的一点是，decoder 应该做到向后兼容，即能够编码先前 encoder 产生的 bitstream。
- 常见的做法：

  - 只修改 encoding 的过程：固定 decoder，对新类的学习能力低
  - 本文的发现：endoer 和 decoder 都学习，固定 entropy model（轻量化）

### 实验设置

- Data-incremental

  - Fintuning on new data
- Rate-incremental

  - 给定压缩率的上下限，fintuning on 不同的上下限，$[\lambda_{low}^{(0)},\lambda_{high}^{(0)}], [\lambda_{low}^{(1)},\lambda_{high}^{(1)}]$
- Metric

  - bpp: bits per pixel
  - PSNR: 峰值信噪比
  - BD-Rate
- 评价指标

  - Backward Compatibility（向后的兼容性）：模型需要可以编码由旧任务生成的 bitstream。（评估旧数据的失真率 Distortion），这里不需要考虑旧类图像在新的 Encoder 上的表现，仅考虑旧的 bitsstream 在 Decoder 上的表现。
  - New-data Performance：新的模型在新数据或者新的 Rate 上的表现应该比旧模型好（评估新数据的失真率 Distortion）
  - 实验设置
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/MVNQbWCNHoTeyFxNl7ScLDjvnj3.png)

### Methods

- 回放图像
  - 收集 examplars： $X_{train}^{(0)}$ and $f_{enc}^{(0)}$ ，利用 $f_{enc}^{(0)}$ 可生成 $Z_{train}^{(0)}$
  - 使用旧任务的 ${\Lambda}^{(0)}$ ，保证 Decoder 能够解码$Z_{train}^{(0)}$
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/Uv82bjGXroiIHIxEO2XcW2gqn5f.png)
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/TLF3bphCroJCATxcOaQcVj1MnSh.png)
  - $\alpha$平衡学习新数据/Rate 和保持向后兼容性

![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/AIH5bpjUXoOPM5xccbSckwm9n8g.png)

![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/SVupb3BWBouoYMxiiyNc4sd2nyd.png)

- 模型结构
  - 具体的模型结构参考：[Variational image compression with a scale hyperprior.](https://arxiv.org/abs/1802.01436)
  - 解耦 Entropy model 和 Decoder model？
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/Npqcbc9NOoiWfHx5cpuctj93nR1.png)

### Results

- Data-IL

![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/P663bYkvDoPZPVxTfvXchcjanIe.png)

- Rate-IL

![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/MxFabAxhZoU6DdxGZIecHGr4n4c.png)

- Ablation Study
  - Ablation Study of Data and KR
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/JRZfb7hxtoRKjyxpMXqcScktnEf.png)
    - 0：pretrained model
    - 1：Fintuning Encoder and Decoder
    - 2：Fintuning Encoder and Decoder + Replay Image
    - 3：KR Loss + Fintuning Encoder and Decoder
    - 4：KR Loss + Fintuning Encoder and Decoder + Replay Image
    - KR Loss 是重放旧模型生成的特征，使用的是 $f_{enc}^{(0)}$
    - Data 是重放旧数据，使用的是 $f_{enc}^{(1)}$
  - Albation Study of Parallel Structure
    ![](https://github.com/jack-chen-2019/liangzc/raw/main/_posts/image_compress/Qpwmb3MIeocfwsxHOrdcMxUUnFg.png)
