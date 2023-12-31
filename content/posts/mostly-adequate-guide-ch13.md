+++
title = "函数式编程指南中文版第13章"
date = 2023-11-21T14:26:00+08:00
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
[返回目录](../mostly-adequate-guide/#mu-lu)
<!--more-->

# 第 13 章: 集大成者的 Monoid

<!-- # Chapter 13: Monoids bring it all together -->

## 狂野的 Combination

<!-- ## Wild combination -->

在本章，我们将通过 [_semigroup_][wiki-semigroup] 介绍 [_monoid_][wiki-monoid]。 如果把数学的抽象比作头发，那么 _monoid_ 就是头发上的泡泡糖，它们捉取各种概念，这些概念横跨多种法则，真正地、象征地将它们集合到一起。它们是联系这些计算的神秘之力，是我们代码库的氧气，是代码运作的根基，量子纠缠的编码。

<!-- In this chapter, we will examine *monoids* by way of *semigroup*. *Monoids* are the bubblegum in the hair of mathematical abstraction. They capture an idea that spans multiple disciplines, figuratively and literally bringing them all together. They are the ominous force that connects all that calculates. The oxygen in our code base, the ground on which it runs, quantum entanglement encoded. -->

_Monoid_ 是关于 combination 的，可什么是 combination？combination 可以表示很多东西，从累加、到相加、到相乘、到选择、到复合、到排序、甚至是求值！我们可以很多的例子，不过接下来我们只会介绍其中一小部分。_Monoid_ 有大量的实例和广泛的应用，本章的主要目的是为你培养好的直觉，好让你可以创造自己的 _monoid_。

<!-- *Monoids* are about combination. But what is combination? It can mean so many things from accumulation to concatenation to multiplication to choice, composition, ordering, even evaluation! We'll see many examples here, but we'll only tip-toe on the foothills of monoid mountain. The instances are plentiful and applications vast. The aim of this chapter is to provide a good intuition so you can make some *monoids* of your own. -->

[wiki-semigroup]: https://zh.wikipedia.org/wiki/%E5%8D%8A%E7%BE%A4
[wiki-monoid]: https://zh.wikipedia.org/wiki/%E5%B9%BA%E5%8D%8A%E7%BE%A4

## 将加法抽象化

<!-- ## Abstracting addition -->

加法有些有趣的特性值得讨论，让我们透过抽象的护目镜来看一看它。

<!-- Addition has some interesting qualities I'd like to discuss. Let's have a look at it through our abstraction goggles. -->

对于初学者而言，加法是一个二元的操作，它接受两个值然后返回一个值作为结果，这些值都是在相同的集合内。

<!-- For starters, it's a binary operation, that is, an operation which takes two values and returns a value, all within the same set. -->

```js
// 加法二元操作
1 + 1 = 2
```

看到了吗？等式左侧的定义域有两个值，等式右侧的值域有一个值，这些值都在相同的数字集合中。一些人会指出，数字“在加法下是闭合的”，既取任意的数字进行加法，其结果将仍是数字。既然数字加法的结果总是数字，这便代表着我们可以将多个加法操作链接起来。

<!-- See? Two values in the domain, one value in the codomain, all the same set - numbers, as it were. Some might say numbers are "closed under addition", meaning the type won't ever change no matter which ones get tossed into the mix. That means we can chain the operation since the result is always another number: -->

```js
// 我们可以将任意数量的数字进行加法操作
1 + 7 + 5 + 4 + ...
```

除此之外（译者注：In addition to that，是一句双关语），我们还获得了结合律，结合律给予我们将操作任意成组。顺带一提，满足结合律的二元操作非常适合并行计算，因为我们可以将计算分块并将它们分发出去。

<!-- In addition to that (what a calculated pun...), we have associativity which buys us the ability to group operations however we please. Incidentally, an associative, binary operation is a recipe for parallel computation because we can chunk and distribute work. -->

```js
// 结合律
(1 + 2) + 3 = 6
1 + (2 + 3) = 6
```

注意不要将结合律与交换律混淆，交换律指的是允许我们改变定义域的值顺序。虽然加法也满足交换律，不过现在我们并不需要关注它，毕竟交换律对于我们的抽象并非必要的，它过于特别了。

<!-- Now, don't go confusing this with commutativity which allows us to rearrange the order. While that holds for addition, we're not particularly interested in that property at the moment - too specific for our abstraction needs. -->

来思考看看，什么属性是我们的抽象父类无论如何都该拥有的？加法特有的什么特性能够被泛化？还有其他的抽象在这些层级之间吗？或是说这些抽象都是同一块？这也正是数学领域先祖们在构思抽象代数的接口所思考的问题。

<!-- Come to think of it, what properties should be in our abstract superclass anyways? What traits are specific to addition and what ones can be generalized? Are there other abstractions amidst this hierarchy or is it all one chunk? It's this kind of thinking that our mathematical forefathers applied when conceiving the interfaces in abstract algebra. -->

那些老派抽象家们碰巧最后将加法抽象为 _[群（group）][wiki-group]_ 的概念。_群_ 包含许多包括负数在内的华而不实的概念。而我们仅关注那些满足结合律的二元操作符，因而我们选择没那么特异化的接口 [_semigroup（半群）_][wiki-semigroup]。_semigroup_ 是带有 `concat` 方法的类型，其中 `concat` 方法即是满足结合律的二元操作符。

[wiki-group]: https://zh.wikipedia.org/wiki/%E7%BE%A4
[wiki-semigroup]: https://zh.wikipedia.org/wiki/%E5%8D%8A%E7%BE%A4

<!-- As it happens, those old school abstractionists landed on the concept of a _group_ when abstracting addition. A _group_ has all the bells and whistles including the concept of negative numbers. Here, we're only interested in that associative binary operator so we'll choose the less specific interface _Semigroup_. A _Semigroup_ is a type with a `concat` method which acts as our associative binary operator. -->

让我们为加法实现这个接口，并命名为 `Sum`：

<!-- Let's implement it for addition and call it `Sum`: -->

```js
const Sum = (x) => ({
	x,
	concat: (other) => Sum(x + other.x),
})
```

留意，我们 `concat` 其他的 `Sum`，且总是返回一个 `Sum`。

<!-- Note we `concat` with some other `Sum` and always return a `Sum`. -->

我用了对象工厂而没有像上面一样用原型方法，主要因为 `Sum` 不是 _pointed_ 的，而且我也不想使用 `new`。不管怎样，它表现如下：

<!-- I've used an object factory here instead of our typical prototype ceremony, primarily because `Sum` is not _pointed_ and we don't want to have to type `new`. Anyways, here it is in action: -->

```js
Sum(1).concat(Sum(3)) // Sum(4)
Sum(4).concat(Sum(37)) // Sum(41)
```

像这样，我们可以面向接口编程，而不是面向实现编程。这些接口来源于群论，而这些理论背后有几百年来的文献支撑。

<!-- Just like that, we can program to an interface, not an implementation. Since this interface comes from group theory it has centuries of literature backing it up. Free docs! -->

正如上文所说，`Sum` 既不是 _pointed_ 也不是 _functor_。考考你，回到之前的章节所讲的定律看看为什么。好吧，我还是直接告诉你：`Sum` 只是持有一个数字，所以我们不能用 `map` 把 `Sum` 持有的数字转化成其他非数字的类型，严格来说这会是一个非常受限的 `map`。

<!-- Now, as mentioned, `Sum` is not _pointed_, nor a _functor_. As an exercise, go back and check the laws to see why. Okay, I'll just tell you: it can only hold a number, so `map` does not make sense here as we cannot transform the underlying value to another type. That would be a very limited `map` indeed! -->

那么，为什么 _semigroup_ 大有用处？因为我们可以置换任意接口实现的实例来得到不同的结果：

<!-- So why is this useful? Well, as with any interface, we can swap out our instance to achieve different results: -->

```js
const Product = (x) => ({ x, concat: (other) => Product(x * other.x) })

const Min = (x) => ({ x, concat: (other) => Min(x < other.x ? x : other.x) })

const Max = (x) => ({ x, concat: (other) => Max(x > other.x ? x : other.x) })
```

这不仅仅适用于数字，再看看其他的类型：

<!-- This isn't limited to numbers, though. Let's see some other types: -->

```js
const Any = (x) => ({ x, concat: (other) => Any(x || other.x) })
const All = (x) => ({ x, concat: (other) => All(x && other.x) })

Any(false).concat(Any(true)) // Any(true)
Any(false).concat(Any(false)) // Any(false)

All(false).concat(All(true)) // All(false)
All(true)
	.concat(All(true)) // All(true)

	[(1, 2)].concat([3, 4]) // [1,2,3,4]

'miracle grow'.concat('n') // miracle grown"

Map({ day: 'night' }).concat(Map({ white: 'nikes' })) // Map({day: 'night', white: 'nikes'})
```

如果你盯着看足够久，这种模式就会像 [_magic eye poster_][wiki-magic-eye] 一样跳到你眼前。它无处不在，在合并数据结构时，在组合逻辑时，在构建字符串时...简直就像我们能够将任何任务一棒子挥进这个基于组合的接口一样。

[wiki-magic-eye]: https://en.wikipedia.org/wiki/Magic_Eye

<!-- If you stare at these long enough the pattern will pop out at you like a magic eye poster. It's everywhere. We're merging data structures, combining logic, building strings...it seems one can bludgeon almost any task into this combination based interface. -->

目前为止我已经用了好几次 `Map`，原谅我没有好好介绍它。`Map` 仅仅包裹了 `Object`，以便我们可以在它之上添加一些额外的方法，而不至于改变内部的结构。

<!-- I've used `Map` a few times now. Pardon me if you two weren't properly introduced. `Map` simply wraps `Object` so we can embellish it with some extra methods without altering the fabric of the universe. -->

## 我喜爱的 functor 都是 semigroup

<!-- ## All my favourite functors are semigroups. -->

我们目前为止所实现 functor 接口的类型，全都也同时实现了 semigroup 的接口。我们先来看看 `Identity` (之前被叫做 Container)：

<!-- The types we've seen so far which implement the functor interface all implement semigroup one as well. Let's look at `Identity` (the artist previously known as Container): -->

```js
Identity.prototype.concat = function (other) {
	return new Identity(this.__value.concat(other.__value))
}

Identity.of(Sum(4)).concat(Identity.of(Sum(1))) // Identity(Sum(5))
Identity.of(4).concat(Identity.of(1)) // TypeError: this.__value.concat is not a function
```

当且仅当 `Identity` 的 `__value` 是 semigroup 时，`Identity` 才是 semigroup 。就像是容易掉落的悬挂滑翔翼一样，只有当它有人乘坐时，它才能够飞起来。

<!-- It is a _semigroup_ if and only if its `__value` is a _semigroup_. Like a butterfingered hang glider, it is one whilst it holds one. -->

其他的类型也有类似的行为：

<!-- Other types have similar behavior: -->

```js
// 与错误处理组合使用
Right(Sum(2)).concat(Right(Sum(3))) // Right(Sum(5))
Right(Sum(2)).concat(Left('some error')) // Left('some error')

// 与异步任务组合使用
Task.of([1, 2]).concat(Task.of([3, 4])) // Task([1,2,3,4])
```

在我们将这些这些 semigroup 嵌套地组合使用时，这将会非常有用。

<!-- This gets particularly useful when we stack these semigroups into a cascading combination: -->

```js
// formValues :: Selector -> IO (Map String String)
// validate :: Map String String -> Either Error (Map String String)

formValues('#signup').map(validate).concat(formValues('#terms').map(validate)) // IO(Right(Map({username: 'andre3000', accepted: true})))
formValues('#signup').map(validate).concat(formValues('#terms').map(validate)) // IO(Left('one must accept our totalitarian agreement'))

serverA.get('/friends').concat(serverB.get('/friends')) // Task([friend1, friend2])

// loadSetting :: String -> Task Error (Maybe (Map String Boolean))
loadSetting('email').concat(loadSetting('general')) // Task(Maybe(Map({backgroundColor: true, autoSave: false})))
```

在上面的例子中，我们将持有 `Map` 的 `Either` 的 `IO` 和验证以及合并表单值方法组合起来。然后，我们用 `Task` 和 `Array` 将多个服务异步调用的结果组合起来。最后，我们把 `Task`, `Maybe` 和 `Map` 堆叠起来，加载、解析和合并多个 setting。

<!-- In the top example, we've combined an `IO` holding an `Either` holding a `Map` to validate and merge form values. Next, we've hit a couple of different servers and combined their results in an async way using `Task` and `Array`. Lastly, we've stacked `Task`, `Maybe`, and `Map` to load, parse, and merge multiple settings. -->

这些也可以通过 `chain` 或者 `ap` 来实现，只是 _semigroup_ 能更准确地表达我们的意图。

<!-- These can be `chain`ed or `ap`'d, but _semigroups_ capture what we'd like to do much more concisely. -->

semigroup 的拓展可以远超 functor。实际上，任何由 semigroup 组成的对象同样也都是 semigroup，如果我们能够 concat 元件，那么我们也能够 concat 由元件组成的整体。

<!-- This extends beyond functors. In fact, it turns out that anything made up entirely of semigroups, is itself, a semigroup: if we can concat the kit, then we can concat the caboodle. -->

```js
const Analytics = (clicks, path, idleTime) => ({
	clicks,
	path,
	idleTime,
	concat: (other) =>
		Analytics(
			clicks.concat(other.clicks),
			path.concat(other.path),
			idleTime.concat(other.idleTime),
		),
})

Analytics(Sum(2), ['/home', '/about'], Right(Max(2000))).concat(
	Analytics(Sum(1), ['/contact'], Right(Max(1000))),
)
// Analytics(Sum(3), ['/home', '/about', '/contact'], Right(Max(2000)))
```

如上，所有的元件都知道怎么和自己组合。事实上，我们能够用 `Map` 类型做到同样的事情：

<!-- See, everything knows how to combine itself nicely. Turns out, we could do the same thing for free just by using the `Map` type: -->

```js
Map({ clicks: Sum(2), path: ['/home', '/about'], idleTime: Right(Max(2000)) }).concat(
	Map({ clicks: Sum(1), path: ['/contact'], idleTime: Right(Max(1000)) }),
)
// Map({clicks: Sum(3), path: ['/home', '/about', '/contact'], idleTime: Right(Max(2000))})
```

我们可以按照喜好堆叠和组合任意数量的 semigroup，这就和往森林里加一棵树，或者往森林大火添一把火一样简单。

<!-- We can stack and combine as many of these as we'd like. It's simply a matter of adding another tree to the forest, or another flame to the forest fire depending on your codebase. -->

semigroup 默认且符合直觉的行为是组合类型持有的值，然而，某些情况下我们希望忽略 semigroup 内持有的值，直接组合两个容器。例如 `Stream`：

<!-- The default, intuitive behavior is to combine what a type is holding, however, there are cases where we ignore what's inside and combine the containers themselves. Consider a type like `Stream`: -->

```js
const submitStream = Stream.fromEvent('click', $('#submit'))
const enterStream = filter((x) => x.key === 'Enter', Stream.fromEvent('keydown', $('#myForm')))

submitStream.concat(enterStream).map(submitForm) // Stream()
```

我们分别捕捉两个事件转换成事件流并把他们组合起来。作为另一选择，我们可以让 `Stream` 持有 semigroup 来组合它们。实际上，每个类型有多种可能的实例。以 `Task` 为例，我们可以通过决定选择较早或较晚的两个来组合它们。我们可以总是选择第一个 `Right` 而不是遇到 `Left` 就短路（`Left` 有忽略错误的效果）。_Alternative_ 接口实现了部分，它的实例通常更关注选择而不是层叠组合。如果你需要类似的功能，再多查阅一些资料是值得的。

<!-- We can combine event streams by capturing events from both as one new stream. Alternatively, we could have combined them by insisting they hold a semigroup. In fact, there are many possible instances for each type. Consider `Task`, we can combine them by choosing the earlier or later of the two. We can always chose the first `Right` instead of short circuiting on `Left` which has the effect of ignoring errors. There is an interface called _Alternative_ which implements some of these, well, alternative instances, typically focused on choice rather than cascading combination. It is worth looking into if you are in need of such functionality. -->

## 空的 Monoid

<!-- ## monoid for nothing -->

我们刚在抽象加法，但和巴比伦人一样，我们还缺了零的概念（它被提到了零次）。

<!-- We were abstracting addition, but like the Babylonians, we lacked the concept of zero (there were zero mentions of it). -->

零和 _identity_ 一样，任何元素与 `0` 相加，最后都得到的都是它自身。抽象地来说，将 `0` 当作某种中立或者 _empty_ 的元素会更好理解。不管零在二元操作的左边或是右边，它的表现都是一样的，这点很重要：

<!-- Zero acts as _identity_ meaning any element added to `0`, will return back that very same element. Abstraction-wise, it's helpful to think of `0` as a kind of neutral or _empty_ element. It's important that it act the same way on the left and right side of our binary operation: -->

```js
// identity
1 + 0 = 1
0 + 1 = 1
```

我们把零的概念命名为 `empty`，然后为它创建一个新的接口。和大部分创业公司一样，我们选择了一个可恶的、无法直达其意的，但却很方便谷歌到的名字：_Monoid_。取任意一个 _semigroup_ 再佐以一个特殊的 _identity_ 就能烹调出 _Monoid_。接下来我们将用其类型来实现一个 `empty`：

<!-- Let's call this concept `empty` and create a new interface with it. Like so many startups, we'll choose a heinously uninformative, yet conveniently googleable name: _Monoid_. The recipe for _Monoid_ is to take any _semigroup_ and add a special _identity_ element. We'll implement that with an `empty` function on the type itself: -->

```js
Array.empty = () => []
String.empty = () => ''
Sum.empty = () => Sum(0)
Product.empty = () => Product(1)
Min.empty = () => Min(Infinity)
Max.empty = () => Max(-Infinity)
All.empty = () => All(true)
Any.empty = () => Any(false)
```

什么情况下 _empty_ 或 _identity_ 值是有用的？这像是在问为什么零有用，就和没问一样。

<!-- When might an empty, identity value prove useful? That's like asking why zero is useful. Like not asking anything at all... -->

当我们什么都没有了，那我们还能用什么来表示？零。我们希望出现多少 bug？零。它是我们对不安全的代码的容忍度，是一个全新的开始，是终极的价标。它能湮灭任何东西或是在关键时刻救我们一命，它是美好的救命稻草，是绝望的深渊。

<!-- When we have nothing else, who can we count on? Zero. How many bugs do we want? Zero. It's our tolerance for unsafe code. A fresh start. The ultimate price tag. It can annihilate everything in its path or save us in a pinch. A golden life saver and a pit of despair. -->

从代码上说，他们是合理的缺省值：

<!-- Codewise, they correspond to sensible defaults: -->

```js
const settings = (prefix="", overrides=[], total=0) => ...

const settings = (prefix=String.empty(), overrides=Array.empty(), total=Sum.empty()) => ...
```

或者在我们没有结果时返回一个有用的值:

<!-- Or to return a useful value when we have nothing else: -->

```js
sum([]) // 0
```

他们同时也是完美的累加器初始值...

<!-- They are also the perfect initial value for an accumulator... -->

## 把房子折叠起来

<!-- ## Folding down the house -->

`concat` 和 `empty` 恰好能完美地填入 `reduce` 的两个参数。事实上，我们可以在不传入 _empty_ 值的情况下 `reduce` 一个 _semigroup_ 的数组，但是你会发现，这将使你陷入岌岌可危的境地：

<!-- It just so happens that `concat` and `empty` fit perfectly in the first two slots of `reduce`. We can actually `reduce` an array of _semigroup_'s down by ignoring the _empty_ value, but as you can see, that leads to a precarious situation: -->

```js
// concat :: Semigroup s => s -> s -> s
const concat = x => y => x.concat(y)

[Sum(1), Sum(2)].reduce(concat) // Sum(3)

[].reduce(concat) // TypeError: Reduce of empty array with no initial value
```

Boom~ 爆炸。就像是带着扭伤的脚踝跑马拉松一样，我们碰上了一个跑步时的异常（译者注：原文为 runtime exception 双关语）。JavaScript 非常乐意我们在起跑之前把手枪绑到我们的鞋子上, 这大概因为它是保守的语言。不过它避免我们死在一个无效的数组上。不然的话，它应该返回什么呢？`NaN`，`false`，`-1`？如果我们可以继续运行我们的程序，我们期望获得一个合适类型的结果。它可能返回一个 `Maybe` 来表示结果可能失败，不过我们还能做得更好。

<!-- Boom goes the dynamite. Like a twisted ankle in a marathon, we have ourselves a runtime exception. JavaScript is more than happy to let us strap pistols to our sneakers before running - it is a conservative sort of language, I suppose, but it stops us dead in our tracks when the array is barren. What could it return anyhow? `NaN`, `false`, `-1`? If we were to continue on in our program, we'd like a result of the right type. It could return a `Maybe` to indicate the possibility of failure, but we can do one better. -->

让我们用柯里化的 `reduce` 写一个安全的函数，这个函数的 `empty` 不再是可选的，我们把它命名为 `fold`:

<!-- Let's use our curried `reduce` function and make a safe version where the `empty` value is not optional. It shall henceforth be known as `fold`: -->

```js
// fold :: Monoid m => m -> [m] -> m
const fold = reduce(concat)
```

初始的 `m` 是我们中立的起始点 `empty` 值，我们再接收 `m` 的数组并把他们压制成像钻石一样美而坚固的值。

<!-- The initial `m` is our `empty` value - our neutral, starting point, then we take an array of `m`'s and crush them down to one beautiful diamond like value. -->

```js
fold(Sum.empty(), [Sum(1), Sum(2)]) // Sum(3)
fold(Sum.empty(), []) // Sum(0)

fold(Any.empty(), [Any(false), Any(true)]) // Any(true)
fold(Any.empty(), []) // Any(false)

fold(Either.of(Max.empty()), [Right(Max(3)), Right(Max(21)), Right(Max(11))]) // Right(Max(21))
fold(Either.of(Max.empty()), [Right(Max(3)), Left('error retrieving value'), Right(Max(11))]) // Left('error retrieving value')

fold(IO.of([]), ['.link', 'a'].map($)) // IO([<a>, <button class="link"/>, <a>])
```

在最后的两个例子里，我们手动制造了一个 `empty` 值，因为我们没办法在类型上定义一个 `empty`，但是并无大碍。拥有类型的语言可以自己解决这个问题，但是在这例子里我们得自己传进去。

<!-- We've provided a manual `empty` value for those last two since we can't define one on the type itself. That's totally fine. Typed languages can figure that out by themselves, but we have to pass it in here. -->

## 不太算 Monoid

<!-- ## Not quite a monoid -->

有一些 _semigroup_ 是不能变成 _monoid_ 的，因为他们没法提供初始值。例如 `First`：

<!-- There are some _semigroups_ that cannot become _monoids_, that is provide an initial value. Look at `First`: -->

```js
const First = (x) => ({ x, concat: (other) => First(x) })

Map({ id: First(123), isPaid: Any(true), points: Sum(13) }).concat(
	Map({ id: First(2242), isPaid: Any(false), points: Sum(1) }),
)
// Map({id: First(123), isPaid: Any(true), points: Sum(14)})
```

我们合并几个账号，并且保持 `First` id 不变，这里我们无法给 `First` 定义一个 `empty` 值。不过这不代表它一无是处。

<!-- We'll merge a couple of accounts and keep the `First` id. There is no way to define an `empty` value for it. Doesn't mean it's not useful. -->

## 大一统理论

<!-- ## Grand unifying theory -->

## 群论还是范畴论

<!-- ## Group theory or Category theory? -->

二元操作的概念在抽象代数里随处可见，它是 _范畴（category）_ 里的主要操作。然而我们不能在没有 _identity_ 的情况下在范畴论中模拟我们的操作。这就是为什么我们先从[群论][wiki-group-theory]的 semigroup 开始讲起，然后当我们获得 _empty_ 后又讲起[范畴论][wiki-category-theory]的 monoid。

[wiki-group-theory]: https://zh.wikipedia.org/wiki/%E7%BE%A4%E8%AE%BA
[wiki-category-theory]: https://zh.wikipedia.org/wiki/%E8%8C%83%E7%95%B4%E8%AE%BA

<!-- The notion of a binary operation is everywhere in abstract algebra. It is, in fact, the primary operation for a _category_. We cannot, however, model our operation in category theory without an _identity_. This is the reason we start with a semi-group from group theory, then jump to a monoid in category theory once we have _empty_. -->

Monoid 来自以 `concat` 为态射，以 `empty` 为 identity 的单对象范畴，组合能够被保证的。

<!-- Monoids form a single object category where the morphism is `concat`, `empty` is the identity, and composition is guaranteed. -->

### 组合作为 Monoid

<!-- ### Composition as a monoid -->

类型 `a -> a` 的函数，它的定义域与值域在相同的集合内，称作 _自同态（endomorphisms）_ `Endo` 的这样的 _Monoid_：

<!-- Functions of type `a -> a`, where the domain is in the same set as the codomain, are called _endomorphisms_. We can make a _monoid_ called `Endo` which captures this idea: -->

```js
const Endo = run => ({
  run,
  concat: other =>
    Endo(compose(run, other.run))
})

Endo.empty = () => Endo(identity)


// in action

// thingDownFlipAndReverse :: Endo [String] -> [String]
const thingDownFlipAndReverse = fold(Endo(() => []), [Endo(reverse), Endo(sort), Endo(append('thing down')])

thingDownFlipAndReverse.run(['let me work it', 'is it worth it?'])
// ['thing down', 'let me work it', 'is it worth it?']
```

由于他们都是相同的类型，我们可以经由 `compose` 来 `concat`，而且结果类型总是相同的。

<!-- Since they are all the same type, we can `concat` via `compose` and the types always line up. -->

### Monad 作为 Monoid

<!-- ### Monad as a monoid -->

你大概留意到 `join` 也是以类似的方式接收两个 _monad_ 然后把它们合并为一个。它同时也是自然转换或者“functor function”。如之前所言，我们能够通过把自然转换作为态射来把 functor 的范畴变成对象的群论。如果我们现在把它特异化成相同类型的 functor: _Endofunctor_，那么有了 `join` Endofunctor 范畴就是 Monoid，同时也能叫做 _Monad_。用代码展示准确的公式需要一些功夫，我建议你去谷歌一下。

<!-- You may have noticed that `join` is an operation which takes two (nested) monads and squashes them down to one in an associative fashion. It is also a natural transformation or "functor function". As previously stated, we can make a category of functors as objects with natural transformations as morphisms. Now, if we specialize it to _Endofunctors_, that is functors of the same type, then `join` provides us with a monoid in the category of Endofunctors also known as a Monad. To show the exact formulation in code takes a little finagling which I encourage you to google, but that's the general idea. -->

### Applicative functor 作为 Monoid

<!-- ### Applicative as a monoid -->

甚至连 applicative functors 也有 Monoid 的公式，它在范畴论里叫做 _lax monoidal functor_。我们可以实现 Monoid 的接口然后再从中还原 `ap`。

<!-- Even applicative functors have a monoidal formulation known in the category theory as a _lax monoidal functor_. We can implement the interface as a monoid and recover `ap` from it: -->

```js
// concat :: f a -> f b -> f [a, b]
// empty :: () -> f ()

// ap :: Functor f => f (a -> b) -> f a -> f b
const ap = compose(
	map(([f, x]) => f(x)),
	concat,
)
```

## 总结

<!-- ## In summary -->

看，一切都是相互联系，或者只是暂时没有互相联系。这个深刻的认识让 _Monoid_ 成为上到大型应用架构下到小块数据的建模工具。当你在应用中遇到累加或者 combination 时，多尝试通过 _Monoid_ 来思考，一旦你做到这点，再拓展到更多的应用，你会惊喜于 _Monoid_ 能建模的东西之多。

<!-- So you see, everything is connected, or can be. This profound realization makes _Monoids_ a powerful modelling tool for broad swaths of app architecture to the tiniest pieces of datum. I encourage you to think of _monoids_ whenever direct accumulation or combination is part of your application, then once you've got that down, start to stretch the definition to more applications (you'd be surprised how much one can model with a _monoid_). -->

## Exercises
