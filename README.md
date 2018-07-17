原文：https://medium.com/@chbchb55/the-importance-of-readable-code-165895e939c7

如何使你的代码可读性更高
--------------------

我们时不时都会读（写）到一些糟糕的代码，当然我们都希望努力提高编码能力而不是仅仅学习一个又一个的新框架。

## 为什么我们需要写优秀的代码，而不是仅仅是有效的代码？

虽然对你的产品或者网站来说性能是非常重要的，但是代码的可读性同样不可忽视。这背后的原因就是：你的代码不是只给电脑看的。
首先最有可能的情况是，最终你都会需要重新阅读你代码的某一部分，这时候，单纯高效的代码并不能帮助你理解代码的逻辑或是修复代码的bug。

其次，如果你在团队里与其他开发者共同工作，那么他们就会阅读你写的代码并且以他们的方式去理解其中的逻辑。为了让这个事情变得更容易，思考如何命名变量和函数，每一行代码的长度以及你代码的结构等事情就变得很重要了。

最后，纯粹让代码更好看。

## 第一部分：如何定义糟糕的代码？

我观点来说，最简单定义糟糕代码的方法，就是尝试阅读代码，就好像它是一个句子或短语。举例来说，下面有几行代码。

![Alt text](https://ws3.sinaimg.cn/large/006tNc79gy1ftcoxt6j6uj30de0adwgl.jpg "traverseUpUntil方法糟糕版本的截图")

上面图片的方法里，当传入一个元素el和一个条件判断函数f之后，返回符合条件函数f的最近父节点p。

根据这一思路，代码应该像自然语言一样可读，第一行代码有三个缺陷。

1. 方法的参数并不能像词语一样易读。
2. 也许el可以被通俗理解为element的缩写，但参数f则不能准确表达其意。
3. 如果你要使用这个方法，读起来有点像：“遍历直到el通过f”（traverse up until el passes f），可能“从el遍历直到通过f”（traverse up until f passes, from el）。诚然，最好的调用这个方法的方式应该是el.traverseUpUntil(f)，但这是另一个问题了。

```let p = el.parentNode```

这是第二行。命名同样有问题，不过这次与变量有关。如果有人读到这行，基本上都能知道p的意思是参数```el```的父节点```parentNode```，然而当我们在其他地方读到```p```呢，因为没有上下文了我们便无法理解它是什么意思。

```while (p.parentNode && !f(p)) {```

在这一行，最主要的问题在于我们无法很直观的知道 ```!f(p)```是什么意思或是他做了什么，因为```f```在这里可以表示任何含义。阅读代码的人应该理解的是```!f(p)```是检查当前节点是否通过该条件，如果是，则停止循环。

```p = p.parentNode```

这一行表达的很明白。

```return p```

因为糟糕的命名并不能百分百清楚什么被返回了。

## 第二部分：让我们改进吧

![Alt text](https://ws4.sinaimg.cn/large/006tNc79gy1ftcp8gcji1j30de07ywib.jpg "好版本的” traverseUpUntil”方法截图")

我们修改了参数的名字：```(el, f) =>``` 变成了 ```(condition, node) =>。``` (you can also do condition => node => which adds an extra layer of useability)。你可能在想为什么使用```node```而不是```element```，以下几个原因。

1. 我们早就习惯了写节点，比方说.parentNode，那么为什么不全部统一呢
2. 比起```element```，```node```更简短并且还不丢失本意。我之所以这么说的原因是因为```node```在所有有```parentNode```属性的所有节点结构中都存在，而不是仅仅html的元素。

接下去我们改进变量的命名：

```let parent = node```

从变量的命名中完全了解它的含义是非常重要的，```p```现在变成了```parent```。也许你已经注意到了我们是从```node.parentNode```开始的，而不是```node```。
这就引导了我们修改下面几行：
```
do {
  parent = parent.parentNode
} while (parent.parentNode && !condition(parent))
```
我们用```do..while```循环取代了常规的```while```循环，这样做的目的是，当运行条件函数后，我们只需要获得父节点一次，而不是相反的方式。```do…while```的方式也同时让代码读起来像文章。
让我们试着阅读它：当父节点的父节点存在并且条件函数不返回true的时候，父节点等于父节点的父节点。看起来有点奇怪，不过这也让我们了解当代码更可读的时候也就更能理解它的作用。

```
return parent
```

很多人选择用通用的ret变量名（或是 returnValue），对于练习如何命名来说并不是一件好事。用恰当的名字来命名返回的内容时就很明显的可以理解什么被返回了。然而有些方法可能很长并且很复杂，在这种情况下，我会建议拆开它变成多个方法，如何任然很复杂，写一些注释会很有帮助。

## 第三部分：简化代码
现在你已经让你的代码可读了。是时候去掉那些没必要的代码了。我相信你们已经注意到了，我们或许完全不需要变量parent。

```
const traverseUpUntil = (condition, node) => {
  do {
    node = node.parentNode
  } while (node.parentNode && !condition(node))
  
  return node
}
```

我做的就是把第一行的parent替换成node，这就绕过了创建parent这没有必要的一步直接进入循环。
但是变量的命名没有问题吗？
“node”命名已经不是对变量最好的描述了，这仅仅是还行的名字，但我们不要仅仅停留在还行。让我们重命名一下，“currentNode”如何？

```
const traverseUpUntil = (condition, currentNode) => {
  do {
    currentNode = currentNode.parentNode
  } while (currentNode.parentNode && !condition(currentNode))
  
  return currentNode
}
```

看上去好多了！现在当我们阅读的时候，currentNode就可以永远表达当前的节点而不是某一个节点。

