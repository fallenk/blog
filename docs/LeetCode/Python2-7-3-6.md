---
title: Python2.7=>3.6
date: 2018-05-19 11:50:58
tags: [python转换] 
category: [Python学习]
---
# 简介
目前Python代码主要分为2.7和3.6版本，注意转换

# 注意点
1. 在2.7中， `print xxx`; 在3.6中： `print(xxx)`
2. 在2.7中，使用`map(xxx)`,之后会自动适应list，但在3.6中需要转换 `list(map(xxx))`；一定要记得转换
3. 学会debug
4. Python3.6 中 `/`代表 除法，如'1/3 = 0.33333333...';而'//'代表地板擦，整除；Python2中相反，所以在np.array记得转换


