# ICICS 2021

一些新词汇：

google monkey

Monkey / monkey bot 

Intelligent Software Testing -continuous learning

XIAO: Tuning code clones at hands of engineers in practice 

AIXcoder:智能编程机器人：http://www.aixcoder.com



### Code signing PKI ecosystem（公钥认证-）

概述：介绍南韩的？

PPT优点：

提出5个research ques问题，然后逐个解答，很有逻辑，

介绍了自己的数据集，介绍图表，

takeways?

英文很正宗



### GAN-based

bypass 

PPt : 汇报者名字加粗

Malware C2 (convert channel) vs traffic

GAN module  / WGAN

pretraining-

reference：

https://www.varonis.com/blog/what-is-c2/

Malware C2： command and control



### OTP- 2FA

one time pin - 验证码

attack

headless browser session

internal attack / remote attack

non-blocking / blocking     keylogger / screenshot



## IOT Security

### disappear  face

infrared interference（红外线的 干扰） / irradiation照射

whitebox low transferability（可移植性）

face detection / adversarial敌手 patches / public weakness

ADAM 算法？

8 face images in different condition

DS： unlike WIDER FACE



还挺有意思的，是把什么二维码之类的东西贴在脸上，以逃脱手机相机的识别？

---

### handwriting

Issues ： chirography difference 人和人笔迹不同，训练集训练，但是攻击者不在训练集中

HIAWARE DEsign

手写传感器？

3peaks,3 strokes=笔画

posture aware analysis

**Threat** Model

pretend as a game app

stylus

(画符游戏？？)

segment detection

speculate推测 accurancy 

Summary（PPT）

通过传感器推测字符吗？相当于识别字符？ 不需要获取权限，在后台运行不会被注意到，

---

### Moat: Model Agnostic Defense against Targeted Poisoning Attacks in Federated Learning

联邦学习：模型 数据驱动

 Label Flipping and Backdoor attacks 

  uses interpretation techniques to measure marginal contribution of individual features

 The aggregation of interpreted values for important features against a baseline input detects the presence of an adversary. 

> This paper discusses a novel defense mechanism using model explanation techniques to detect adversaries’ presence besides their number and strategy. This analysis does not require any prior knowledge or auxiliary synthesis. It uses Shapley algorithms, game-theory-based mathematical formulations that give the features’ marginal contributions based on their importance

**联邦学习**

概念

​		多个企业，拥有不可共享的非完全重叠的数据集，其中一方有标签数据集，将数据经过加密，用于训练同一个模型，并得到适合自己的模型，反馈

采用同态加密技术，这个过程中，各方的原始数据，以及数据加密态都没有被传输。交互部分，双方通过损失中间结果，用同态加密的机制进行交互，模型训练完之后，会各自得到一个模型，各自的模型会部署在各自的一方，就是如果我只提供了3个特征，那么我只有3个特征的模型，只提供2个特征，就只有2个特征的模型，任何一方的模型都没法单独去应用，只有共同应用的时候，才能进行决策。

>  *同态加密（英語：Homomorphic encryption）是一种加密形式，它允许人们对密文进行特定形式的代数运算得到仍然是加密的结果，将其解密所得到的结果与对明文进行同样的运算结果一样。 换言之，这项技术令人们可以在加密的数据中进行诸如检索、比较等操作，得出正确的结果，而在整个处理过程中无需对数据进行解密。* 

应用场景

- *银行+监管，联合反洗钱建模*
- *互联网+银行，联合信贷风控建模*
- *互联网+保险，联合权益定价建模*
- *互联网+零售，联合客户价值建模*

reference：

https://blog.csdn.net/weixin_45439861/article/details/100670390?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-5.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-5.no_search_link

优势

	保护隐私不泄露 （找出用户的交集，但是不能泄露差集，通过 RSA 和 Hash 的机制，保证双方最终只用到交集部分，且差集部分不向对方泄露。）
- *数据隔离：联邦学习的整套机制在合作过程中，数据不会传递到外部。*
- *无损：通过联邦学习分散建模的效果和把数据合在一起建模的效果对比，几乎是无损的。*  （为什么会有损？什么情况会有损，有什么损，什么影响？）
- *对等：合作过程中，合作双方是对等的，不存在一方主导另外一方。*
- *共同获益：无论数据源方，还是数据应用方，都能获取相应的价值。*

安全风险



联邦学习分类：

- 横向联邦学习（横向，按 用户维度 切分）

  用户特征重叠很多，用户重叠很少

  e.g., 北京 的银行和 上海的银行

  

- 纵向联邦学习（纵向，按 特征维度 切分）

  用户重叠很多，特征重叠很少

  e.g., 北京 的银行和 北京的电商

  

- 联邦迁移学习

   在两个数据集的用户与用户特征重叠都较少的情况下，不对数据进行切分，而可以利用迁移学习来克服数据或标签不足的情况。

  

  北京的银行 和 美国的电商

   引入迁移学习，来解决单边数据规模小和标签样本少的问题，从而提升模型的效果。 

Ques：

训练后想达到一个什么样的效果呢？



reference:

https://blog.csdn.net/cao812755156/article/details/89598410



https://zhuanlan.zhihu.com/p/100688371