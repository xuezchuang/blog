title: OrderIndependentTransparency
author: xuezc
tags:
  - opengl
  - graph
description: oit 次序无关的半透明渲染
categories:
  - graph
abbrlink: 2db854d1
date: 2023-05-15 15:53:00
---
# 什么是顺序无关渲染
在3D渲染中，物体的渲染是按一定的顺序渲染的，这也就可能导致半透明的物体先于不透明的物体渲染，结果就是可能出现半透明物体后的物体由于深度遮挡而没有渲染出来。对于这种情况通常会先渲染所有的不透明物体再渲染半透明物体或者按深度进行排序来解决。但这样仍然无法解决半透明物体之间的透明效果渲染错误问题，特别是物体之间存在交叉无法通过简单的排序来解决。于是就有一些用专门来解决半透明物体渲染算法，OIT算法即Order Independent Transparency（顺序无关的半透明渲染）。Depth Peeling是众多OIT算法里可以得到精确blending结果的一个，在非游戏的3d应用场景中应该还是很有价值的。
# Depth Peeling
Single Depth Peeling 顾名思义，就是通过多次绘制，每次绘制剥离离相机最靠近的一层，像剥洋葱一样层层剥开，按顺序混合就得到了精确的混合结果。既然有Single Depth Peeling，还有一种优化版本就是Dual Depth Peeling，从前后两个方向剥离，不在本次讨论的范围.

<img src="/images/oit1.png">