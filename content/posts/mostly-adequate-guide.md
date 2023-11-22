+++
title = "函数式编程指南中文版"
date = 2023-11-21T13:57:22+08:00
draft = false

[taxonomies]
categories = ["学而时习之"]
tags = ["programming", "javascript"]

[extra]
lang = "zh_CN"
toc = true
copy = true
math = true
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
+++

This is the Simplified Chinese translation of *[mostly-adequate-guide](https://github.com/DrBoolean/mostly-adequate-guide)*, thank Professor [Franklin Risby](https://github.com/DrBoolean) for his great work!
<!--more-->
{{ figure(src="/img/cover.png", style="height: 50%; width: 50%") }}

# 关于本书

这本书的主题是函数范式（functional paradigm），我们将使用 JavaScript 这门世界上最流行的函数式编程语言来讲述这一主题。有人可能会觉得选择 JavaScript 并不明智，因为当前的主流观点认为它是一门命令式（imperative）的语言，并不适合用来讲函数式。但我认为，这是学习函数式编程的最好方式，因为：

 * **你很有可能在日常工作中使用它**

    这让你有机会在实际的编程过程中学以致用，而不是在空闲时间用一门深奥的函数式编程语言做一些玩具性质的项目。

 * **你不必从头学起就能开始编写程序**

    在纯函数式编程语言中，你必须使用 monad 才能打印变量或者读取 DOM 节点。JavaScript 则简单得多，可以作弊走捷径，因为毕竟我们的目的是学写纯函数式代码。JavaScript 也更容易入门，因为它是一门混合范式的语言，你随时可以在感觉吃力的时候回退到原有的编程习惯上去。

 * **这门语言完全有能力书写高级的函数式代码**

    只需借助一到两个微型类库，JavaScript 就能模拟 Scala 或 Haskell 这类语言的全部特性。虽然面向对象编程（Object-oriented programing）主导着业界，但很明显这种范式在 JavaScript 里非常笨拙，用起来就像在高速公路上露营或者穿着橡胶套鞋跳踢踏舞一样。我们不得不到处使用 `bind` 以免 `this` 不知不觉地变了，语言里没有类可以用（目前还没有），我们还发明了各种变通方法来应对忘记调用 `new` 关键字后的怪异行为，私有成员只能通过闭包（closure）才能实现，等等。对大多数人来说，函数式编程看起来更加自然。

以上说明，强类型的函数式语言毫无疑问将会成为本书所示范式的最佳试验场。JavaScript 是我们学习这种范式的一种手段，将它应用于什么地方则完全取决于你自己。幸运的是，所有的接口都是数学的，因而也是普适的。最终你会发现你习惯了 swiftz、scalaz、haskell 和 purescript，以及其他各种数学偏向的语言。

### Gitbook (更好的阅读体验)

* [在线阅读](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/)
* [下载EPUB](https://www.gitbook.com/download/epub/book/llh911001/mostly-adequate-guide-chinese)
* [下载Mobi (Kindle)](https://www.gitbook.com/download/mobi/book/llh911001/mostly-adequate-guide-chinese)

# 目录

## 第 1 部分

* [第 1 章: 我们在做什么？](../mostly-adequate-guide-ch1/)
  * [介绍](../mostly-adequate-guide-ch1/#jie-shao)
  * [一个简单例子](../mostly-adequate-guide-ch1/#yi-ge-jian-dan-li-zi)
* [第 2 章: 一等公民的函数](../mostly-adequate-guide-ch2/)
  * [快速概览](../mostly-adequate-guide-ch2/#kuai-su-gai-lan)
  * [为何钟爱一等公民](../mostly-adequate-guide-ch2/#wei-he-zhong-ai-yi-deng-gong-min)
* [第 3 章: 纯函数的好处](../mostly-adequate-guide-ch3/)
  * [再次强调“纯”](../mostly-adequate-guide-ch3/#zai-ci-qiang-diao-chun)
  * [副作用可能包括...](../mostly-adequate-guide-ch3/#fu-zuo-yong-ke-neng-bao-gua)
  * [八年级数学](../mostly-adequate-guide-ch3/#ba-nian-ji-shu-xue)
  * [追求“纯”的理由](../mostly-adequate-guide-ch3/#zhui-qiu-chun-de-li-you)
  * [总结](../mostly-adequate-guide-ch3/#zong-jie)
* [第 4 章: 柯里化（curry）](../mostly-adequate-guide-ch4/)
  * [不可或缺的 curry](../mostly-adequate-guide-ch4/#bu-ke-huo-que-de-curry)
  * [不仅仅是双关语／咖喱](../mostly-adequate-guide-ch4/#bu-jin-jin-shi-shuang-guan-yu-ka-li)
  * [总结](../mostly-adequate-guide-ch4/#zong-jie)
* [第 5 章: 代码组合（compose）](../mostly-adequate-guide-ch5/)
  * [函数饲养](../mostly-adequate-guide-ch5/#han-shu-si-yang)
  * [pointfree](../mostly-adequate-guide-ch5/#pointfree)
  * [debug](../mostly-adequate-guide-ch5/#debug)
  * [范畴学](../mostly-adequate-guide-ch5/#fan-chou-xue)
  * [总结](../mostly-adequate-guide-ch5/#zong-jie)
* [第 6章: 示例应用](../mostly-adequate-guide-ch6/)
  * [声明式代码](../mostly-adequate-guide-ch6/#sheng-ming-shi-dai-ma)
  * [一个函数式的 flickr](../mostly-adequate-guide-ch6/#yi-ge-han-shu-shi-de-flickr)
  * [有原则的重构](../mostly-adequate-guide-ch6/#you-yuan-ze-de-zhong-gou)
  * [总结](../mostly-adequate-guide-ch6/#zong-jie)

## 第 2 部分

* [第 7 章: Hindley-Milner 类型签名](../mostly-adequate-guide-ch7/)
  * [初识类型](../mostly-adequate-guide-ch7/#chu-shi-lei-xing)
  * [神秘的传奇故事](../mostly-adequate-guide-ch7/#shen-mi-de-chuan-qi-gu-shi)
  * [缩小可能性范围](../mostly-adequate-guide-ch7/#suo-xiao-ke-neng-xing-fan-wei)
  * [自由定理](../mostly-adequate-guide-ch7/#zi-you-ding-li)
  * [总结](../mostly-adequate-guide-ch7/#zong-jie)
* [第 8 章: 特百惠](../mostly-adequate-guide-ch8/)
  * [强大的容器](../mostly-adequate-guide-ch8/#qiang-da-de-rong-qi)
  * [第一个 functor](../mostly-adequate-guide-ch8/#di-yi-ge-functor)
  * [薛定谔的 Maybe](../mostly-adequate-guide-ch8/#xie-ding-e-de-maybe)
  * [“纯”错误处理](../mostly-adequate-guide-ch8/#chun-cuo-wu-chu-li)
  * [王老先生有作用...](../mostly-adequate-guide-ch8/#wang-lao-xian-sheng-you-zuo-yong)
  * [异步任务](../mostly-adequate-guide-ch8/#yi-bu-ren-wu)
  * [一点理论](../mostly-adequate-guide-ch8/#yi-dian-li-lun)
  * [总结](../mostly-adequate-guide-ch8/#zong-jie)
* [第 9 章: Monad](../mostly-adequate-guide-ch9/)
  * [pointed functor](../mostly-adequate-guide-ch9/#pointed-functor)
  * [混合比喻](../mostly-adequate-guide-ch9/#hun-he-bi-yu)
  * [chain 函数](../mostly-adequate-guide-ch9/#chain-han-shu)
  * [理论](../mostly-adequate-guide-ch9/#li-lun)
  * [总结](../mostly-adequate-guide-ch9/#zong-jie)
* [第 10 章: Applicative Functor](../mostly-adequate-guide-ch10/)
  * [应用 applicative functor](../mostly-adequate-guide-ch10/#ying-yong-applicative-functor)
  * [瓶中之船](../mostly-adequate-guide-ch10/#ping-zhong-zhi-chuan)
  * [协调与激励](../mostly-adequate-guide-ch10/#xie-diao-yu-ji-li)
  * [lift](../mostly-adequate-guide-ch10/#lift)
  * [免费开瓶器](../mostly-adequate-guide-ch10/#mian-fei-kai-ping-qi)
  * [定律](../mostly-adequate-guide-ch10/#ding-lu)
  * [总结](../mostly-adequate-guide-ch10/#zong-jie)
* [第 11 章: 再转换一次，就很自然](../mostly-adequate-guide-ch11/)
  * [令人生厌的嵌套](../mostly-adequate-guide-ch11/#ling-ren-sheng-yan-de-qian-tao)
  * [一场情景喜剧](../mostly-adequate-guide-ch11/#yi-chang-qing-jing-xi-ju)
  * [全都很自然](../mostly-adequate-guide-ch11/#quan-du-hen-zi-ran)
  * [有原则的类型转换](../mostly-adequate-guide-ch11/#you-yuan-ze-de-lei-xing-zhuan-huan)
  * [方法狂](../mostly-adequate-guide-ch11/#fang-fa-kuang)
  * [同构的 JavaScript](../mostly-adequate-guide-ch11/#tong-gou-de-javascript)
  * [更加宽泛的定义](../mostly-adequate-guide-ch11/#geng-jia-kuan-fan-de-ding-yi)
  * [实现单层嵌套的方法](../mostly-adequate-guide-ch11/#shi-xian-dan-ceng-qian-tao-de-fang-fa)
  * [总结](../mostly-adequate-guide-ch11/#zong-jie)
* [第 12 章: 遍历](../mostly-adequate-guide-ch12/)
  * [类型与类型](../mostly-adequate-guide-ch12/#lei-xing-yu-lei-xing)
  * [类型风水](../mostly-adequate-guide-ch12/#lei-xing-feng-shui)
  * [作用组合](../mostly-adequate-guide-ch12/#zuo-yong-zu-he)
  * [类型的华尔兹](../mostly-adequate-guide-ch12/#lei-xing-de-hua-er-zi)
  * [定律](../mostly-adequate-guide-ch12/#ding-lu)
  * [同一律](../mostly-adequate-guide-ch12/#tong-yi-lu-identity)
  * [组合](../mostly-adequate-guide-ch12/#zu-he-composition)
  * [自然](../mostly-adequate-guide-ch12/#zi-ran-naturality)
  * [总结](../mostly-adequate-guide-ch12/#zong-jie)
* [第 13 章：集大成者的 Monoid](../mostly-adequate-guide-ch13/)
  * [狂野的 Combination](../mostly-adequate-guide-ch13/#kuang-ye-de-combination)
  * [将加法抽象化](../mostly-adequate-guide-ch13/#jiang-jia-fa-chou-xiang-hua)
  * [我喜爱的 functor 都是 semigroup](../mostly-adequate-guide-ch13/#wo-xi-ai-de-functor-du-shi-semigroup)
  * [空的 Monoid](../mostly-adequate-guide-ch13/#kong-de-monoid)
  * [把房子折叠起来](../mostly-adequate-guide-ch13/#ba-fang-zi-zhe-die-qi-lai)
  * [不太算 Monoid](../mostly-adequate-guide-ch13/#bu-tai-suan-monoid)
  * [大一统理论](../mostly-adequate-guide-ch13/#da-yi-tong-li-lun)
  * [群论还是范畴论](../mostly-adequate-guide-ch13/#qun-lun-huan-shi-fan-chou-lun)
  * [总结](../mostly-adequate-guide-ch13/#zong-jie)
* [Appendix A: Essential Functions Support](../mostly-adequate-guide-appendix-a/)
* [Appendix B: Algebraic Structures Support](../mostly-adequate-guide-appendix-b/)
* [Appendix C: Pointfree Utilities](../mostly-adequate-guide-appendix-c/)

# 未来计划

* 第 1 部分是基础知识。这是初版草稿，所以我会及时更正发现的的错误。欢迎提供帮助！
* 第 2 部分讲述类型类（type class），比如 functor 和 monad，最后会讲到到 traversable。我希望能塞进来一些 monad transformer 相关的知识，再写一个纯函数的应用。
* 第 3 部分将开始游走于编程实践与学院学究之间。我们将学习 comonad、f-algebra、free monad、yoneda 以及其他一些范畴学概念。

