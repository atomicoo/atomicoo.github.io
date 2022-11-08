# 简历

---


## 联系方式
- 手机：19859213959 
- Email：atomicoo95@gmail.com 
- QQ/微信号：793071559/Joee1995 

---

## 个人信息

 - 周志洋/男/1995 
 - 硕士/厦门大学人工智能系/自然语言处理实验室（导师：史晓东 教授） 
 - Github：https://github.com/atomicoo 
 - 期望职位：语音合成算法工程师/自然语言处理工程师 
 - 期望薪资：税前月薪 20k~25k，特别喜欢的公司可例外 
 - 期望城市：厦门/深圳/杭州/苏州/上海 

---

## 项目经历
### 厦门大学自然语言处理实验室

#### 藏语语音合成项目（2020年）
藏语（小语种）语音合成项目是实验室与西藏大学的一个合作项目。此项目是我个人独立完成的一个项目。在完成这个项目的过程中，最困难的问题：西藏大学所提供的藏语语音数据集的质量非常差，不仅多个不同说话人的音频混杂在一起，而且大多数音频都含有不同程度的噪声（录音环境包括但不限于街道、教室、集市等）；采取的方案：先利用多说话人识别技术挑选出音色相对接近的部分音频，然后通过引入语音风格编码模块实现模型在多说话人数据集上进行训练时的语音风格自适应，此外还引入了语音数据增强、超分辨率重建等技术以及特殊的损失函数设计来优化语音合成性能。最终的效果：词汇发音准确无误，但在韵律方面有所缺乏（考虑到数据集本身的质量不佳，这样的效果是完全可以接受的）。


#### 汉英语音翻译评测项目（2019年）
汉英语音翻译评测项目是第十五届全国机器翻译大会（CCMT2019）的一项评测任务。我在此项目中负责生成语音伪数据的任务。由于举办方为了增加评测任务的难度，因此在提供数据的数量与质量上都有所缺陷，为了解决这一问题，团队决定采用生成语音伪数据的方式进行数据增强。在完成这一任务的过程中，最困难的问题：通过语音合成模型生成的伪语音与真实语音在数据分布上的不一致性；采取的方案：引入生成对抗网络（GAN），调整语音合成模型，使其生成的伪语音与真实语音在数据分布上尽可能趋向于一致；最终的效果：在性能指标（BLUE）上提升了 ~0.7 个百分点，帮助团队在该评测任务中获得第一名的成绩。

#### 多语种语音翻译项目（2021年，进行中）

多语种语音翻译项目是实验室与科大讯飞的一个合作项目。我在此项目中负责多个语种的语音合成端的模型支持，主要包括英语、普通话/汉语、俄语、维吾尔语和藏语。

#### 其他项目

除了上述项目，我在硕士期间还负责或者参与了其他几个项目，包括：语音标注系统项目、Arxiv 论文数据分析项目、简历匹配项目等。


### 厦门多维树信息科技有限公司

#### 产品经理决策系统项目（2020~2021年）
产品经理决策系统项目是实验室与厦门多维树信息科技的一个合作项目。我在此项目中负责利用 NLP 技术进行大数据分析，并进一步构建出垂直领域（宠物电商领域）知识图谱。该系统用于辅助产品经理进行产品研发或改进方向的决策，作为核心程序员，我所构建的宠物电商领域知识图谱是该系统得以正常运作的关键基石。

---

## 开源项目与作品
### 开源项目
 - [Tacotron2](https://github.com/atomicoo/tacotron2-mandarin)：基于 Tacotron-2 模型的普通话/中文语音合成，TensorFlow 版（近期正在完成的 PyTorch 版见 [Tacotron2-PyTorch](https://github.com/atomicoo/Tacotron2-PyTorch)）。
 - [ParallelTTS](https://github.com/atomicoo/ParallelTTS)：并行语音合成项目，目前已在英语、普通话/中文、日语、韩语、俄语和藏语等多个语种上完成测试。
 - [EnhanceIMG](https://github.com/atomicoo/EnhanceIMG)：图像领域算法探索（考虑到当前语音领域所使用的声学特征多为声谱图，某些图像领域的技巧完全有可能运用到语音领域中，因此个人对图像领域的算法也会有一些探索）。

### 演讲和讲义
 - 近期某次实验室组会分享：[ParallelTTS：快速并行语音合成](https://atomicoo.github.io/slides/%E5%BF%AB%E9%80%9F%E5%B9%B6%E8%A1%8C%E8%AF%AD%E9%9F%B3%E5%90%88%E6%88%90.pdf) 

## 技能清单
- 开发环境：Linux/Windows 
- 版本管理、文档和自动化部署工具：Git/Sphinx 
- 编程语言：Python/Java 
- 深度学习框架：PyTorch/TensorFlow 
- 语音合成相关：Tacotron/DCTTS/FastSpeech 
- Web相关：Flask/VueJS 
- 数据库相关：MySQL/Neo4j 

---

## 致谢
感谢您花时间阅读我的简历，期待能有机会与您共事。