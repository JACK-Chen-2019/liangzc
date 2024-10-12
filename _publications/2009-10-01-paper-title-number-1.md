---
title: "Reformulating Classification as Image-Class Matching for Class Incremental Learning"
collection: publications
permalink: /publication/2009-10-01-paper-title-number-1
excerpt: 'Published in: IEEE Transactions on Circuits and Systems for Video Technology'
date: 2024.09
venue: 'Journal 1'
paperurl: 'https://ieeexplore.ieee.org/document/10681473'
citation: '@ARTICLE{10681473,
  author={Hu, Yusong and Liang, Zichen and Liu, Xialei and Hou, Qibin and Cheng, Ming-Ming},
  journal={IEEE Transactions on Circuits and Systems for Video Technology}, 
  title={Reformulating Classification as Image-Class Matching for Class Incremental Learning}, 
  year={2024},
  volume={},
  number={},
  pages={1-1},
  keywords={Transformers;Incremental learning;Feature extraction;Semantics;Computer vision;Pipelines;Continuing education;Class incremental learning;Image classification;Vision transformer;Matching},
  doi={10.1109/TCSVT.2024.3462734}}'
---

<a href='[http://academicpages.github.io/files/paper1.pdf](https://ieeexplore.ieee.org/document/10681473)'>Download paper here</a>

Abstract:
Class incremental learning (CIL) sequentially increases the number of classes, which often leads to catastrophic forgetting when fine-tuning on new classes. Existing approaches typically employ linear classifiers and expand them to accommodate new classes. However, conducting conventional classification inherently introduces feature drift in the image space upon the introduction of new classifiers, potentially disrupting the established distributions, and resulting in forgetting. In this paper, we propose a novel insight to reformulate the conventional classification as image-class matching (ICM) to mitigate the disruption. ICM independently encodes the image and the category and allows for the sharing of a matching classifier across all tasks, effectively stabilizing the feature space during the CIL process. To apply ICM to CIL, we introduce the Binary Matching Classification (BMC) framework, which employs cross attention to encode the matching relationship between images and each category to predict matching scores. When learning new tasks, BMC only requires the addition of category inputs without any structural changes. Moreover, we present a series of strategies to enhance the adaptation of BMC to CIL. Through simple regularization, our BMC framework achieves outstanding performance on various benchmarks including CIFAR-100, ImageNet-100, and ImageNet-1000 datasets. Our code is available at https://github.com/Ethanhuhuhu/BMC.

Code will be avaliable at https://github.com/Ethanhuhuhu/BMC

@ARTICLE{10681473,
  author={Hu, Yusong and Liang, Zichen and Liu, Xialei and Hou, Qibin and Cheng, Ming-Ming},
  journal={IEEE Transactions on Circuits and Systems for Video Technology}, 
  title={Reformulating Classification as Image-Class Matching for Class Incremental Learning}, 
  year={2024},
  volume={},
  number={},
  pages={1-1},
  keywords={Transformers;Incremental learning;Feature extraction;Semantics;Computer vision;Pipelines;Continuing education;Class incremental learning;Image classification;Vision transformer;Matching},
  doi={10.1109/TCSVT.2024.3462734}}
