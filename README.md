# "中国法研杯"司法人工智能挑战赛数据说明

## 一、简介

法律智能旨在赋予机器阅读理解法律文本与定量分析案例的能力，完成罪名预测、法律条款推荐、刑期预测等具有实际应用需求的任务，有望辅助法官、律师等人士更加高效地进行法律判决。近年来，以深度学习和自然语言处理为代表的人工智能技术取得巨大突破，也开始在法律智能领域崭露头角，受到学术界和产业界的广泛关注。

为了促进法律智能相关技术的发展，在最高人民法院信息中心、共青团中央青年发展部的指导下，中国司法大数据研究院、中国中文信息学会、中电科系统团委联合清华大学、北京大学、中国科学院软件研究所共同举办“2018中国‘法研杯’法律智能挑战赛（CAIL2018）”。挑战赛将提供海量的刑事法律文书数据作为数据集，旨在为研究者提供学术交流平台，推动语言理解和人工智能领域技术在法律领域的应用，促进法律人工智能事业的发展。每年比赛结束后将举办技术交流和颁奖活动。诚邀学术界和工业界的研究者和开发者积极参与该挑战赛！


## 二、任务说明

### 2.1 介绍

* 任务一（罪名预测）：根据刑事法律文书中的案情描述和事实部分，预测被告人被判的罪名；
* 任务二（法条推荐）：根据刑事法律文书中的案情描述和事实部分，预测本案涉及的相关法条；
* 任务三（刑期预测）：根据刑事法律文书中的案情描述和事实部分，预测被告人的刑期长短。

参赛者可选择一个或者多个任务参与挑战赛。同时，为了鼓励参赛者参与到更多的任务中，组委会将单独奖励参与更多任务的参赛者。

### 2.2 数据介绍

本次挑战赛所使用的数据集是来自“[中国裁判文书网](http://wenshu.court.gov.cn/)”公开的刑事法律文书，其中每份数据由法律文书中的案情描述和事实部分组成，同时也包括每个案件所涉及的法条、被告人被判的罪名和刑期长短等要素。

数据集共包括`268万刑法法律文书`，共涉及[202条罪名](meta/accu.txt)，[183条法条](meta/law.txt)，刑期长短包括**0-25年、无期、死刑**。

我们将先后发布CAIL2018-Small和CAIL2018-Large两组数据集。CAIL2018-Small包括19.6万份文书样例，直接在该网站发布，包括15万训练集，1.6万验证集和3万测试集。这部分数据可以[注册下载](http://cail.cipsc.org.cn)，供参赛者前期训练和测试。

比赛开始2-3周后（具体时间请关注比赛新闻），我们将通过网络下载向有资格的参赛队伍定向发布CAIL2018-Large数据集，包括150万文书样例。最后，剩余90万份文书将作为第一阶段的测试数据CAIL2018-Large-test。

#### 2.2.1 字段及意义
数据利用json格式储存，每一行为一条数据，每条数据均为一个字典。

* **fact**: 事实描述  
* **meta**: 标注信息，标注信息中包括:   
    * **criminals**: 被告(数据中均只含一个被告)  
    * **punish\_of\_money**: 罚款(单位：元)
    * **accusation**: 罪名  
    * **relevant\_articles**: 相关法条  
    * **term\_of\_imprisonment**: 刑期  
        刑期格式(单位：月)
        * **death\_penalty**: 是否死刑  
        * **life\_imprisonment**: 是否无期
        * **imprisonment**: 有期徒刑刑期

```
这里是简单的一条数据展示:  
{   
	"fact": "2015年11月5日上午，被告人胡某在平湖市乍浦镇的嘉兴市多凌金牛制衣有限公司车间内，与被害人孙某因工作琐事发生口角，后被告人胡某用木制坐垫打伤被害人孙某左腹部。经平湖公安司法鉴定中心鉴定：孙某的左腹部损伤已达重伤二级。",   
	"meta": 
	{  
		"relevant_articles": [234],  
		"accusation": ["故意伤害"], 
		"criminals": ["段某"],  
		"term_of_imprisonment": 
		{  
			"death_penalty": false,  
			"imprisonment": 12,  
			"life_imprisonment": false
		}
	}
}
```

### 2.3 评价方法

本次挑战赛使用的数据集均为来自中国裁判文书网上的刑事法律文书，标准答案是案件的判决结果。我们提供了评测时使用的评分程序共选手使用，评测方法、环境和模型提交说明请看[链接](https://github.com/thunlp/CAIL2018)。

每项任务满分100分，下面将对三项任务的评价方法分别进行说明：

#### 2.3.1 任务一、任务二

任务一（罪名预测）、任务二（法条推荐）两项任务将采用分类任务中的微平均F1值（Micro-F1-measure）和宏平均F1值（Macro-F1-measure）作为评价指标，其计算方式为：

![f1](pic/f1.png)

则任务的最终分数为：

![score1](pic/score_1.png)

#### 2.3.2 任务三

任务三（刑期预测）将采用下列公式，根据预测出的刑期与案件标准刑期之间的差值距离作为评价指标。设预测出的刑期为`lp`，标准答案为`la`，则

![v](pic/v.png)

```
若v≤0.2，则score=1；
若0.2<v≤0.4，则score=0.8
……
以此类推。
```
**特殊的情况**

若案件刑期的标准答案为**死刑**或**无期**，则需预测出的刑期与案件标准刑期完全相同才可得1分，否则为0，即：

```
如果la == 死刑或无期：
若lp == la，则score=1；
否则score=0；
```

最后，将任务三所有测试点的分数相加并除以测试点总数乘以100作为任务三的评价得分：

![score3](pic/score_3.png)

#### 2.3.3 三项任务总分的计算方式

每个任务的满分均为100，则总分为：

![score_all](pic/score_all.png)


### 2.4 基线系统

竞赛组织方稍后会提供了两个开源的针对不同任务的基线系统（LibSVM、RNN）。

## FAQ

### 0. 有没有选手交流的平台？

选手交流QQ群：237633234。

### 1. 评测是否需要上传代码？

代码上传不是必须的，我们不会保存选手上传的任何文件。

选手上传的模型和文件应按照[代码提交说明](https://github.com/thunlp/CAIL2018)进行上传，只需符合要求并可以进行正常评测即可。

### 2. 上传后测试机的环境是怎么样的，如是否有相应的python包，是否有gpu，各个软件和第三方库的版本信息等。是否允许选手提出增加第三方依赖的需求？

我们主要支持python的编译环境，有GPU（Tesla P100 16G）。如果需要C++（服务器版本为5.4.0）或者Java（服务器版本为1.8.0_171），可以参考[代码提交说明](https://github.com/thunlp/CAIL2018)进行编译上传。

现有环境包括：

```
python3.5.2
cuda 9.0
cudnn 7.0

numpy 1.14.3
scipy 1.1
gensim 3.4
sklearn 0.19.1
pandas 0.22.0
xgboost 0.71
Cython 0.28.2
fasttext 0.8.3
joblib 0.11
GPUtil 1.3.0
ruamel.base 1.0.0

pyltp 0.2.1
jieba 0.39

Keras 2.1.6
Theano 1.0.1
tensorflow 1.7
pytorch 0.3.1
```

如果有其他需要的环境，请联系比赛管理员进行安装。

### 3. 上传代码后是否有测试时间限制？

原则上没有评测时间限制，但如果我们发现选手的程序4小时没有输出结果，会手动终止，并与选手联系。

### 4. 上传代码以及其他文件是否有大小限制，如本地训练好的模型可能很大？

通过网站上传代码和模型的文件大小限制为200MB，一般来说正常的模型不会超过200M。
如果需要上传更大的模型或者代码，请联系比赛管理人员进行沟通。

### 5. 比赛是否允许用外部数据？

如需使用外部数据，可以在上传文件时同时进行。需要注意的是评测环境为禁止网络连接。

### 6. 是否允许人手工构建的一些知识性数据？

我们欢迎选手使用各种方式来解决本次比赛的任务，进而不会对使用的方法进行任何限制。

