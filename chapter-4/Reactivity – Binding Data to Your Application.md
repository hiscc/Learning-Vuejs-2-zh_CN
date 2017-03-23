# 响应式 -- 为你的方程式绑定数据

在前一章， 你学习了 Vue.js 最重要的一个概念： 组件。 知道了如何你创建组件并注册、 调用、 重用组件
。 你理解了单文件组件并在购物清单和番茄钟方程式中应用。

在本章， 我们将深入数据绑定。 我们在前面已经讨论过它了， 你应该对它不再陌生了。 在我们的组件中你将使用所有可能的方式来绑定数据。

总结起来就是：
* 回顾数据绑定语法
* 在方程式里应用数据绑定
* 在相同的模板里用不同的数据来遍历数组并渲染单个元素
* 在我们的方程式中回顾并使用数据绑定和事件绑定简写方式

## 回顾数据绑定
在第一章我们已经讨论了数据绑定和响应式。 现在你已经了解了数据绑定就是从数据到视觉层的传播机制， 反之亦然。 在本章， 我们将在方程式中仔细复习所有的数据绑定方式。

## 启动数据

想象我们现在有以下 **HTML** 代码片段：

```
<div id="hello"></div>
```

同时还有如下 *JavaScript* 对象：

```
var data = {
  msg: 'Hello'
};
```

我们怎样在页面渲染所有数据呢？ 我们又是怎样在 **HTML** 内获取它们呢？ 事实上， 我们已经在前两章就解决了这两个问题了。 当然我们可以再来一次。

> "Repetitio est mater studiorum"

如果你已经对数据插值十分熟悉， 可以先跳过这部分直接到表达式和过滤器部分。

所以， 我们应该怎样在 div 标签内填充 *msg* 的值呢？ 如果我们用 jQuery 的老办法将是这样的：

```
$(".hello").text(data.msg);
```

但是， 如果你在方程式运行期间改变 *msg* 的值并期望改变传递给 DOM , 你必须手动更新。 简单改变 *data.msg* 的值， 什么都不会发生。

```
var data = {
  msg: 'Hello'
};
$("#hello").text(data.msg);
data.msg = 'Bye';
```

在 *<div>* 中将毫无疑问出现 *Hello*， 在 JSFiddle 查看效果 https://jsfiddle.net/chudaol/uevnd0e4/。

通过 Vue, 最简单的实现方式是使用 *{{}}*， 在我们的事例中， 我们这样写：

```
<div id="hello">{{ msg }}</div>
```

*<div>* 的内容尽管被绑定到了 *msg* 数据上， 当 *msg* 变化时， *div* 的内容会自动更新。 在 JSFiddle 查看效果  https://jsfiddle.net/chudaol/xuvqotmq/1/ 。 *data.msg* 也是随着 Vue 实例而变化的。 页面中的值将是一个新值。

这依旧是单向的数据绑定。 如果我们在 DOM 中改变值， 数据不会发生改变。 因此， 当我们值需要数据的值出现在 DOM 中时这将是完美的解决方案。

现在， 我们非常清楚地知道： 如果我们只想在模板内使用数据对象的值， 我们应该使用 *{{}}*。

我们来继续实现我们的番茄钟方程式。 请在[chapter4/pomodoro](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter4/pomodoro) 查看当前的解决方案。 如果你运行了 *npm run dev* 后，你的页面应该像这样：

![](imgs/4-1.png)

实现我们的番茄钟

简单看一眼页面， 我们可以看出这里缺少什么。

页面中缺少时间， 小猫， 还有番茄钟的状态。 小猫出现的逻辑依靠与番茄钟的状态。 我们来为番茄钟增加状态和时间。

## 为番茄钟增加状态标题

首先， 我们应该确定这个元素应该属于哪个组件。 看看我们的四个组件。 很显然它属于 *StateTitleComponent* 组件。 查看下面代码， 它实际上已经实现了挑剔绑定：

```
//StateTitleComponent.vue
<template>
<h3>{{ title }}</h3>
</template>

<style scoped>
</style>

<script>
</script>
```

很好！ 在前面的章节里， 我们已经做了大部分工作。 现在我们只需为此增加必须的数据绑定。 在这个组件的 script 标签内， 我们来增加包含 *title* 特性的数据对象。 现在， 我们给它硬编码一个可能的值然后再决定怎么去改变它。 你想怎样改变呢？ *Work!* 还是 *Rest!* ？ 我知道答案， 我们来添加如下代码：

```
//StateTitleComponent.vue
<script>
export default {
  data () {
    return {
      title: 'Learning Vue.js!'
    }
  }
}
</script>
```

我们先就这样。 我们将在后面的 methods 和 event 控制中继续完善。

### 练习

用相同的方法为番茄钟增加事件， 可以先硬编码。

## 使用表达式和过滤器

在前面的事例中， 我们在 *{{}}* 中用了简单的属性键来插值。 事实上， Vue 在花括号里还有更多语法。 我们来看看。

## 表达式

超出意外， 但是 Vue 完全支持在花括号写表达式！ 我们来用任意一个番茄钟组件试试这些表达式。 你可以在这里查看一些实践 [chapter4/pomodoro2](https://github.com/PacktPublishing/Learning-Vuejs-2/tree/master/chapter4/pomodoro2) 。

随便在一个组件内尝试下， 例如 *StateTitleComponent.vue* 文件。 我们来添加一些 JavaScript 表达式：

```
{{ Math.pow(5, 2) }}
```

事实上， 你需要取消下面这些注释：

```
//StateTitleComponent.vue
<!--<p>-->
  <!--{{ Math.pow(5, 2) }}-->
<!--</p>-->
```

你将会在页面中看到 *25*。 好的。 我们用表达式来替换番茄钟内的一些简单数据绑定。 例如， 在 *CountdownComponent* 组件模板中， 两个指令， *min* 和 *sec* ，就可以被替换成一个表达式， 现在它是这样的：

```
//CountdownComponent.vue
<template>
  <div class="well">
    <div class="pomodoro-timer">
      <span>{{ min }}</span>:<span>{{ sec }}</span>
    </div>
  </div>
</template>
```

我们可以用下面的代码进行替换：

```
//CountdownComponent.vue
<template>
  <div class="well">
    <div class="pomodoro-timer">
      <span>{{ min + ':' + sec }}</span>
    </div>
  </div>
</template>
```

我们还可以在哪里加点表达式呢？ 我们再看看 *StateTitleComponent* 。现在， 我们需要硬编码标题。 我们知道， 但是， 标题依赖于番茄钟的状态。 如果是在 *working* 状态， 它将显示 **work!**， 否则应该显示 **Rest!**。 我们来创建一个叫 *isworking* 的特性， 把它分配在 App.vue 组件内， 因为它看起来更像是个全局方程式状态。 我们将重用 *StateTitleComponent* 组件的 *props* 特性。 因此， 打开 App.vue 为 *isworking* 添加一个布尔值， 设为真：

```
//App.vue
<...>
window.data = {
  kittens: true,
  isworking: true
};
export default {
  <...>
  data () {
    return window.data
    }
}
```

我们在 *StateTitleComponent* 中重用属性， 为每个可能的值增加两个字符串属性， 最后， 在模板内增加判断状态来渲染标题的表达式。 因此， 组件的脚本看起来是这样的：

```
//StateTitleComponent.vue
<script>
  export default {
    data () {
      return {
        workingtitle: 'Work!',
        restingtitle: 'Rest!'
      }
    },
    props: ['isworking']
  }
</script>
```

我们用 *isworking* 属性来有条件地渲染标题。 因此， *StateTitleComponent* 的模板是这样的：

```
<template>
  <div>
    <h3>
      {{ isworking ? workingtitle : restingtitle }}
    </h3>
  </div>
</template>
```

刷新页面。 奇怪的是， 竟然标题显示的是 *Rest!* ， 可我们明明设置 *isworking* 为真值的。 那是因为我们忘记在 *App.vue* 中调用这个属性值！ 打开 *App.vue* 组件增加下面的代码：

```
<state-title-component v-bind:isworking="isworking"><state-title-component>
```

现在， 如果你再查看页面， 当前的标题就显示为 *Work!* 了！ 如果你打开控制台并输入 *data.isworking = false*, 你将看到标题改变。

如果 *isworking* 特性为假， 标题就是 *Rest!*， 截图在下面：

![](imgs/4-2.png)

如果 *isworking* 特性为真， 标题就是 *Work!*， 截图在下面：

![](imgs/4-3.png)

## 过滤器

除了在模板中使用表达式， 我们还可以使用过滤器来转换表达式的值。 过滤器就是写函数。 我们创建它们并用管道符 **|** 应用它。 如果你创建了一个让字符大写的过滤器并命名为 uppercase ，为了使用它， 仅仅是在管道符后调用：

```
<h3> {{title | lowercase }} </h3>
```

你可以链接很多过滤器， 例如， 如果你有了 A、 B 、C 三个过滤器， 你可以这样做 *{{ key | A | B | C }}*。 过滤器通过 *Vue.filter* 语法创建。 我们来创建我们的 *lowercase* 过滤器：

```
//main.js
Vue.filter('lowercase', (key) => {
  return key.toLowerCase()
})
```

我们在我们的番茄钟的 *App.vue* 组件中应用。 为了使用过滤器， 我们应该网模板插值传入 “Pomodoro” 字符串。 我们应该传入 JavaScript 字符串表达式， 并使用管道符来应用：

```
<template>
  <...>
    <h2>
      <span>{{ 'Pomodoro' | lowercase }}</span>
      <controls-component></controls-component>
    </h2>
  <...>
</template>
```

查看页面； **Pomodoro** 标题将以小写语法出现。

我们来回顾 *CountdownTimer* 组件。 现在， 这里只有一个硬编码的值。 但是但当方程式充满功能， 值应该由方程式计算而来。 值的范围为 0 到 60， 值可以是 **20：40** ,但不应该比 10 小。 例如， 现在只有 1 分钟 5 秒， 它会显示 1： 5， 这样不统一。 我们期望它是这样的 *01： 05* 。 所以， 我们需要一个左边填充的过滤器！ 我们来创建一个。

在 *main.js* 文件中添加一个左边填充过滤器：

```
//main.js
Vue.filter('leftpad', (value) => {
  if (value >= 10) {
    return value
  }
    return '0' + value
})
```

打开 *CountdownComponent* 组件， 来把 *min* 和 *sec* 分割成不同的插值， 并为它们都添加一个过滤器：

```
//CountdownComponent.vue
<template>
  <div class="well">
    <div class="pomodoro-timer">
      <span>{{ min | leftpad }}:{{ sec | leftpad }}</span>
    </div>
  </div>
</template>
```

用 1 和 5 来替代 *min* 和 *sec*， 分别查看。 数字被 “0” 填充好。

## 练习

创建两个过滤器， *uppercase* 和 *addspace*， 在发你番茄钟里应用：

* *uppercase* 过滤器必须确切地自说明
* *addspace* 过滤器必须为给定的字符串右边增加空间

不要忘记 **Pomodoro** 不是关键， 所以在插值括号里它不应被认为是一个字符串！ 标题应该像下面截图所示：

![](imgs/4-4.png)

番茄钟标题在应用过滤器前后的变化

在[ chapter4/pomodoro3 ](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter4/pomodoro3)。

## 回顾并应用指令

在前面的部分， 我们知道了方程式中插值并绑定到可见视图层。 尽管过滤器语法非常棒并提供了数据更改的能力， 它依然有很多限制。 例如， 用 *{{}}* 实现下面的效果：

* 在输入框内使用插值属性， 并对相应的对应值应用变化
* 为 data 绑定特殊的元素特性
* 依条件来渲染元素
* 遍历数组并渲染一些数组组件
* 为元素创建事件监听器

我们来试试第一个。 例如打开购物清单方程式( [chapter4/shopping-list ](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter4/shopping-list) )。 在 *App.vue* 模板中创建输入元素并设置 *{{ title }}*：

```
<template>
  <div id="app" class="container">
    <h2>{{ title }}</h2>
    <input type="text" value="{{ title }}">
    <add-item-component></add-item-component>
    <...>
  </div>
</template>
```

啊偶！ 错误， 满地都是。 **在特性中插值已经被移除了**。 在 Vue 2.0 中你可以简单地在特性中使用插值了吗？ 是的， 也不是。 在特性中插值将不会出现错误但会使标题中的值清零。 在 Vue 2.0 中， 也就是先行版， 实现这种行为， 我们必须使用指令。

### 注意

**指令** 是拥有 *v-* 前缀的特殊特性元素。 为什么是 *v-* 呢？因为 Vue 指令提供了简单的语法， 它有一套丰富的文本插值。 它们在数据每次变化时强有力地响应可见视图的特殊行为。

## 用 v-model 指令启动双向数据绑定

双向绑定会将数据变化传递给 DOM 层， 同时在 DOM 中的绑定数据也会传递回数据。 用这样的方法来向 DOM 绑定数据， 我们可以使用 *v-model* 指令。

我们确信你依然记得第一章的 *v-model* 指令吧：

```
<input type="text" v-model="title">
```

以这种方式， 标题的值将出现在 input 中， 如果你在 input 中输入一些值， 相应的变化将立即应用给数据并在页面中所有的插值得到相应。

仅仅是用 *v-model* 来替换 handlebars 标记。

试着在输入框中输入一些值。 你可以看到标题值得变化！

记住， 这个指令只能应用给下面的元素：

* input
* select
* textarea

试着删除所有代码。 我们的主要目标是用标题组件改变标题。

## 组件间的双向绑定

记得在前面的章节里， 组件间双向的绑定不可以简单地通过 *v-model* 来实现。 由于架构原因， Vue 阻止了子类简单改变父级的作用域。

我们在子极组件中使用事件系统来改变购物清单标题。

我们将在这一章再做一次。 等过几段之后我们将到 *v-on* 指令再谈。

## 用 v-bind 指令绑定特性

*v-bind* 指令允许我们用表达式绑定一个元素的特性或组件属性。 为了给具体的特性应用， 我们使用冒号分隔符：

```
v-bind：attribute
```

例如：
* v-bind:src = "src"
* v-bind:class = "className"

表达式可 “” 中重写。 data 也可以被使用， 就像在前面的例子一样。 我们在 *KittenCompnent* 中添加小猫图片。 打开我们的番茄钟方程式 [chapter4/pomodoro3](https://github.com/PacktPublishing/Learning-Vuejs-2/tree/master/chapter4/pomodoro3) 。

打开 *KittenCompnent* ， 添加 *catimgsrc* 为组件数据， 用 *v-bind* 语法把 *src* 绑定到图片模板：

```
<template>
  <div class="well">
    <img v-bind:src="catImgSrc" />
  </div>
</template>
<style scoped>
</style>
<script>
  export default {
    data () {
      return {
        catimgsrc: "http://thecatapi.com/api/images/get?size=med"
      }
    }
  }
</script>
```

打开页面， 燥起来吧！

![](imgs/4-5.png)

运用数据特性后了 KittenCompnent 组件

## 用 v-if 和 v-show 指令来条件渲染

如果你关注了前面的部分， 如果我让你条件渲染一些东东， 你可能在插值括号里使用 JavaScript 表达式。

但是， 试试在一些整个组件内来条件渲染一些元素。 它不像在括号里的表达式那样简单。

*v-if* 指令允许条件式地渲染整个元素， 它可以是一个依赖一些条件的组件元素。 条件可以是任何表达式并使用数据属性。 例如， 我们可以这样做：

```
<div v-if="1 < 5">hello</div>
```

或者：

```
<div v-if="Math.random() * 10 < 6">hello</div>
```
或者：

```
<div v-if="new Date().getHours() >= 16">Beer Time!</div>
```

或者使用组件的数据：

```
<template>
  <div>
    <h1 v-if="!isadmin">Beer Time!</h1>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        isadmin: false
      }
      }
  }
</script>
```

*v-show* 特性也是一样的。 唯一的不同点在于 *v-if* 将通过相应的条件来选择是否渲染元素， 而 *v-show* 特性将一直渲染元素， 只是在值为 *false* 时应用 *display： none* 。 我们来看看差别。 在 [ chapter4/beer-time](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter4/beer-time) 内打开 *beer - time* 项目， 运行 *npm run dev* 。 打开 *App.vue* 组件用 *true/false* 值来试验， 试着用 *v-show* 来替代 *v-if*。 打开开发者工具， 检查 **element** 选项卡。

我们先来看看用 *v-if* 指令的效果。

当条件值为真时， 效果如期； 元素被渲染出现在页面内：

![](imgs/4-6.png)

当条件值为真时， 效果如期。

当条件值为假时，元素不被渲染：

![](imgs/4-7.png)

当条件值为假时，元素不被渲染。

注意当条件不被满足时， 相应的元素一点也不会被渲染。

我们我看看 *v-show* 指令的效果。当条件为真时， 和使用 *v-if* 一样：

![](imgs/4-8.png)

使用 *v-show* 来条件渲染。 条件为真。

现在我们来看看当条件为假时， *v-show* 将发生什么：

![](imgs/4-9.png)

使用 *v-show* 来条件渲染。 条件为假。

在这个事例中， 条件为真时， 效果一致， 但当条件为假时， 元素也被渲染出来但加上了 *display: none* 属性。

你如何去决定使用哪一个指令呢？ 在第一次渲染时， 如果条件为假， *v-if* 指令一点也不会渲染元素， 因此减少了初始的计算消耗。 但是， 如果属性在运行期间频繁改变， 渲染元素的成本将会比设置 *display:none* 更昂贵。 因此在频繁改变属性时使用 *v-show* ， 其它情况使用 *v-if*。

我们回到番茄钟方程式。 *KittensCompnent* 组件将会依赖于发你番茄钟的状态而被条件式地渲染。 所以， 在 [chapter4/pomodoro4](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter4/pomodoro4) 中打开你的番茄钟方程式。

你觉得应该使用哪个指令？ *v-if* 还是 *v-show* ？ 我们来分析一下。 站在客观的角度， 这个元素应该在初始化时是可视的吗？ 答案是否定的， 因为在初始渲染时， 用户才开始打开番茄钟。 可能使用 *v-if* 更好点。 但是我们来分析另一种情况 -- 频繁地切换状态来使 kittens 组件可见/不可见。 浙江发生在番茄钟的倒计时中， 对吗？ 在 15-20 分钟后或在 5 分钟后重置计时， 实际上这也不是很频繁， 渲染带来的代价也不高
。 对于这个例子， 我的意见是， 用那个都行。 我们使用 *v-show* 。 打开 *App.vue* 文件向 *kittens-component* 应用 *v-show* 指令：

```
<template>
  <div id="app" class="container">
    <...>
    <transition name="fade">
      <kittens-component v-show="!isworking"></kittens-component>
    </transition>
  </div>
</template>
```
打开页面， 试着在开发者工具内切换 *data.isworking* 的值， 观察效果。

## 用 v-for 指令来遍历数组

你可能记得我们用 *v-for* 指令来遍历数组：

```
<div v-for = item in items>
  item
</div>
```

或者在组件内：

```
<component v-for = item in items v-bind:componentitem="item"></component>
```

对于数组内的每一个项， 浙江渲染一个组件并绑定组件内项的值。 当然， 你应该记得 “” 的绑定语法可以随意使用。

### Tip

别忘了在我们使用的绑定语法应该在组件 *data* 内呈现！

顺便看一个例子， 我们的购物清单方程式 [chapter4/shopping-list](https://github.com/PacktPublishing/Learning-Vuejs-2/tree/master/chapter4/shopping-list)。 它已经在 *ItemsComponent* 内使用了 *v-for* 语法：

```
<template>
  <ul>
    <item-component v-for="item in items" :item="item"></item-component>
  </ul>
</template>
```

*ItemComponent*， 也需要声明 *props* ：

```
<script>
  export default {
    props: ['item']
  }
</script>
```

我们来做点有趣的事。 直到现在我们依然只是在处理单个的购物清单。 想象一下你想要为不同的目的建立不同的购物清单。 例如， 你可能要在平常杂货采购中需要一份平常的购物清单。 在节日时你可能需要不同的购物清单。 你可能会在买房子时要一份不同的购物清单。 我们这就用 Vue 中强力的可重用组件来实现我们的购物清单方程式！ 我们将使用 *Bootstrap* 的选项卡面板来展示； 更多信息，请查阅 http://getbootstrap.com/javascript/#tabs 。

在你的 IDE 内打开[购物清单方程式](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter4/shopping-list) 。

首先， 我们增加 Bootstrap 的 JavaScript 文件和 jQuery , 因为 bootstrap  依赖它们。 在 *index.html* 内手动添加：

```
<body>
  <...>
  <script src="https://code.jquery.com/jquery-3.1.0.js"></script>
  <script
    src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js">
  </script>
  <...>
</body>
```

现在我们为我们将做的工作建立一个步骤总览：
1. 首先， 我们需要创建组件。 叫它 *ShoppingListComponent* , 把我们的当前 *App.vue* 内容放到这里面。
2. 我们新的 *ShoppingListComponent* 应该包含 title 和 items 的 *props* 特性。
3. ItemComponent 应该从 *props* 特性内接收 *items* , 而非把它硬编码。
4. 在 *App* 组件数据中， 我们先硬编码一个购物清单的数组， 每一项都应有标题， 数组项， ID。
5. *App.vue* 应该导入 *ShoppingListComponent*, 在模板内， 遍历　*shoppinglists* 数组， 建立一个 *html/jade* 的选项卡结构。

好了， 我们开始吧！
