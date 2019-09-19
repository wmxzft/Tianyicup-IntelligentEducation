# “添翼杯”人工智能创新应用大赛-智慧教育赛道 



## 赛题背景
随着人工智能(AI)的发展，“AI＋教育”“智慧课堂”等名词逐渐出现在大众视野，越来越多的学校将人工智能助手融入课堂，当下中国正逐步进入“智慧教育”时代。在传统课堂中，由于时间和精力的限制，老师和家长无法兼顾学生的学习状态和学业进展，不会关注大量对于学生能反应其真实问题和情况的数据。

智慧教育通过将传统教育行业的场景和当下最新的人工智能算法紧密结合，深度挖掘学生在各个知识点上的历史答题表现数据，最终预测学生在考试中的分数表现。
## 赛题描述
请参赛选手，利用比赛对应训练集提供的学生信息、考试知识点信息、考试总得分信息等建立模型，预测测试集中学生在指定考试中的成绩总分，预测目标如下：

* 初赛：利用初中最后一年的相关考试和考点信息，预测初中最后一学期倒数第二、第三次考试的成绩。
* 复赛：利用初中 4 年中的相关考试和考点信息，预测初中最后一学期最后一次考试的的成绩。
## 数据下载
[初赛数据下载（右键保存下载）](https://www.kesci.com/urls/740cd3de)

## 算法架构
![算法架构](https://github.com/yzh1994414/Tianyicup-IntelligentEducation/blob/master/pictures/Algorithm%20architecture.png)<br>

## 第一部分模型

#### 建模思路：将成绩预测问题视为回归问题进行分析。

##### 通过EDA发现大部分同学的成绩是相对稳定的，而影响其成绩变化的主要的原因是由于试卷分布的不同造成的，即不同试卷考了不同的内容导致同学的成绩发生变化，通过降维的手段对每一次考试的高维稀疏知识点进行降维处理。

#### 特征工程：使用全量的考试成绩通过统计的方式对每一位学生进行“成绩画像”的构建，如：score_mean、score_cv、score_std、score_peak、score_pluse等（score_peak：峰值因子，score_pluse:脉冲值，表示波形是否存在冲击指标，可以理解成学生是否具有偏离正常发挥的高分冲击指标现象）

#### 降维思路：将8门课视作1门课，每门课的每次考试只是在于考试点不同。将8门课的所有考试点concat，构成一门大的科目，（可以理解成这是一门大的《通识》课，包含了各个科目的各个考试点），然后利用NMF降维算法对这门《通识》课进行降维处理。
##### NMF-非负矩阵分解，可以对一个非负数矩阵分解成X = W.H的形式，而这种基于基向量组合的表示形式具有很直观的语义解释，它反映了人类思维中“局部构成整体”的概念。# 

## 第二部分模型

#### 建模思路： 将成绩预测问题视为一种短期时间序列预测的问题。

##### 即认为学生的成绩会发生成绩上升/下滑，通过构建各科目成绩的趋势性、稳定性的特征如： rank_diff_before、rank_diff_long、rank_std、rank_mean等特征。其中，rank_diff_before是指学生前一次考试排名变化的情况，rank_diff_long是指学生前N次考试排名变化的情况）

#### 数据集划分：使用全量的考试成绩情况作为使用，通过一个大小为8的滑动窗口对全量考试进行特征构建。

#### 时序化思路：根据course_exam文件中出现的顺序决定其时序关系. 如： 第n个出现的exam，对应该course下的第n场exam

## 第三部分模型



#### 首先对成绩、试卷考点分布、学生考点掌握整体进行数学抽象
##### 定义：
##### score : m \* n (exam_number * student_numbers) 			试卷-学生成绩矩阵
##### exam : m \* s+ (exam_number * course_section)  			试卷-考点分布矩阵
##### stu : 1+s \* n (course_section \* student_numbers)  学生-考点掌握矩阵

###### 其中， m为各科考试次数 n为学生数目 s为各科考点数

### 建模思路：Argmin <stu> (Σ(score - exam\*stu)^2)

