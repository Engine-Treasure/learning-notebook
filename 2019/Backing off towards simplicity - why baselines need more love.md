# ﻿Backing off towards simplicity - why baselines need more love

- When we lose accurate baselines, we lose our ability to accurately measure our progress over time.
- 4 Keys:
  - Adopt a baseline and give it the care that it deserves
  - Ensure the baseline is fast and well tuned  to provide as a bed for rapid experimentation
  - Take deliberate and reasoned steps forward towards the state of art or more complex models
  - Unless you have strong proof that it's necessary, don't sacrifice speed (不要牺牲速度)
- Adopt a baseline
  - Adopt a baseline to not only help the field but to also provide a grounded domain in which to hone your skills - you might surprise not just yourself by resesearchers across the community :)
  - Even if the Goliaths in the space have access to an armada of GPUs, you can still help advance the field forward (I promise!) and there are many counter examples to show that raw compute is no guarantee they'll beat you
- The issue with complex models
  - 复杂模型对领域进步是有害的. 惯常的思路是: 增加参数等方式使模型过拟合, 然后增加 regularization 来防止过拟合. 在模型越来越复杂的过程中, 缺乏对哪些复杂性是必要的的清醒认识, 是巨大的隐患
  - 复杂的模型能更有效, 但也使得去理解哪一部分是真正有效的变得更加困难. 于是就有了 ablation analysis 就显得更重要了, 不能在迷途上越走越远
  - 快速迭代是实现创新和发现的关键手段, 但是以模型复杂性为代价的情况下, 不建议
  - 更复杂的模型不见得就能取得更好的结果
- The value of baselines
  - Baselines provide a sanity check against improvements, an easy avenue for the curious to begin to explore, and a potential foundation for future innovation to be built upon.
  - 基线由于易于实现和理解, 常用于教育目的. 当基线为了性能而舍弃了简易性, 必须重新思考基线是否具有指导意义. 如果答案是否定的, 改进后的基线就不那么有用了, 它的结果同样如此.
  - 研究员们积极地优化他们的模型, 而不是基线. 基线可能都是事后才想到要去对比的, 甚至可能只是其他论文中的结果的拷贝, 这样的基线显得人工意味十足, 古老而腐朽, 没有价值.
  - When we lose accurate baselines, we lose our ability to accurately measure our progress over time.
- Strategically (战略意义上) improving baselines to state of the art
  - 最简单的最基本的, 不要让模型比所需更复杂.
  - 用现有的基线, 或者自己写. 但是确保它足够简单和快.
  - 在上述基线的基础上, 去 tuning hyperparameters extensively (调参), trying a variety of regularization techniques (正则化), sanity checking against bugs and potentially flawed assumptions (查漏补缺), and delving into the "boring" data processing in detail (检查数据处理). (按照 AllenNLP 的观点, 以上一些步骤应在写模型之前就写好测试)
  - As it is fast, you can spend many runs tuning your hyperparameters. 快, 意味着可以运行更多次, 更多地调参
  - As it is simple, bugs and flawed assumptions become easier to find as the model isn't powerful enough to hide it from you. 简单, 意味着更容易找到 bug 和漏洞
  - 一次, 只修改一个组件, 去发现这个组件的影响
- 人类思维/直觉是有害的
  - Attention 刚被使用的时候, 效果还不如使用更多的隐层单元, 一些人因此认为它没用, 但是被打脸了.
  - RNN 曾和序列问题划上了等号, 但新进的模型, 比如 CNN 改版和 Transfomer 证明不是这样的.
  - 不是人类不够智慧, 是计算机的世界和人类世界有太多的不同.
  - 附上笛卡尔科学研究法 (大胆假设, 小心求证):
    - 先提出问题, 但不要预先设定结论
    - 进行实验
    - 从实验中得到结论和解释: 结论是实验得到的, 不是头脑中固有的
    - 将结论推广并且普遍化
    - 在实践中找出新的问题, 如此循环往复
  - 笛卡尔总结的拉瓦锡的研究方法
    - 理性判断: 不接受自己不清楚的真理. 对一个命题, 要根据自己的判断, 确定有无可疑之处, 任何有可疑之处的命题都不会是真理
    - 化繁为简, 化整为零: 对于复杂问题, 尽量多分解为多个简单的小问题来研究, 一个个分开解决
    - 先易后难: 在解决上述小问题时, 应该按照先易后难的词序, 逐步解决
    - 归纳综合: 解决每个小问题之后, 再综合起来. 看看是否彻底解决了原来的问题.