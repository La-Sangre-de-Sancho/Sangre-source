---
title: 心理统计（2）
date: 2025-3-15 12:12:12
categories: 为数不多的知识
tag: Psychological Statistics
cover: https://pic1.imgdb.cn/item/67d5322e88c538a9b5be972b.jpg
---

### 3.15, written by Sancho

## 非参分析

|               |                    | 对应非参数方法                                                                                  | 对应非参数方法                                   |
|:-------------:|:------------------ |:---------------------------------------------------------------------------------------- |:----------------------------------------- |
| 检测目的          | 基于正态理论的检验          | 顺序变量Ordinal                                                                              | 称名变量Nominal                               |
| 2独立样本         | 独立测量t-test         | Mann-Whitney U（Wilcoxon Rank-sum）；K-S Z test；Moses Extreme Reactions； Wald-Wofowitz runs | 卡方独立性检验                                   |
| 2配对样本         | 配对t-test           | Wilcoxon signed-rank（T）; sign test                                                       | Sign test;  McNemar; Marginal homogeneity |
| 多样本           | One-Way ANOVA      | Kruskal-Wallis(independent) Friedman(dependent)                                          | 卡方拟合优度检验；Cochron' s Q(binorm)             |
| 比较由两种不同因素分类的组 | Two-Way ANOVA      | Friedman two-way analysis of variance                                                    | 卡方独立性检验                                   |
| 两变量线性关联       | 皮尔逊（Pearson）线性回归模型 | Spearman rank correlation coefficient; Kendall’s tau                                     | 卡方独立性检验                                   |

### 一些很重要的点

#### 关于独立样本和配对样本

一定重点关注样本的配对性！例如：

> 小明最近因为考试而焦虑得失眠。为了改善睡眠质量，小明每天做 15min 的正念冥想练习，连续做了一周，他觉得自己的睡眠质量确实照之前有所改善。目前我们获得了小明在实施冥想训练前 1 周和后 1 周的睡眠时长数据 

这里就是独立而非配对的：虽然出自一个个体的前后测量，但是由于效应变化所以无法将前后七天配对。故这里采用适用于独立样本的Mann-Whitney U而不使用Wilcoxon T-test

#### Wilcoxon Signed-Rank(T-test) & Wilcoxon Rank Sum

Wilcoxon Signed-Rank，又被称为Wilcoxon T-test、符号秩次检验法，用于两配对样本的非参分析。

Wilcoxon Rank Sum，更常叫做Mann-Whitney U test，用于两独立样本的非参分析。

值得注意的是，代码直接求统计量时，由于调用一次代码之后按照你输入的顺序求解一次，故要颠倒顺序使用两次取最小的T/U值：

```r
wilcox.test(x, y, alternative = 'two.sided', paired = T, exact = F, correct = F)
wilcox.test(y, x, alternative = 'two.sided', paired = T, exact = F, correct = F)
```

## ANCOVA & Latin-Square

#### ANCOVA

加入协变量（连续变量）

关键前提：

> 协变量与自变量独立
> 
> 协变量与自变量无相互作用
> 
> 当然还有方差同质性检验和独立性检验，前者可以从MANOVA（）结果中看到，后者源自于实验设计故理论上不需要检验

```r
# assumption 1
data3 <- read.csv("dataHW2_Q13(1).csv")
attach(data3)
result1 <- aov(delicious ~ as.factor(screen)) #这里的as.factor意义重大
result2 <- aov(delicious ~ as.factor(format))
summary(result1)
summary(result2)
detach(data3)
# assumption 2
attach(data3)
summary(aov(rating ~ delicious*screen + delicious*format))
detach(data3)
# assumption 3
#直接通过MANOVA（）可以得到结果，也可以：
attach(anxiety)
library(car)
leveneTest(posttest ~ group)
detach(anxiety)
```

#### Latin Square

用来消除两个干扰因素（b&c）的影响

|     | c1  | c2  | c3  |
|:---:|:---:|:---:|:---:|
| b1  | a1  | a2  | a3  |
| b2  | a3  | a1  | a2  |
| b3  | a2  | a3  | a1  |

> Latin Square Design有些像3x3x3， 但是没把3x3x3的所有条件表征完全。
> eg.考察三种不同的复习方式对于学生学业考试成绩的影响（a：记忆策略；b：实验日区分（周一周二周三）；c：实验日内时间段区分（早中晚）
> 时间成为covariate，但是这里也是categorical的，所以不便于使用ANCOVA

一个重要假设：b&c不会组成unique combination
