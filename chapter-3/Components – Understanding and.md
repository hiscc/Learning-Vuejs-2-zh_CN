# Components – Understanding and Using

在前面一章， 你已经知道了 Vue.js 是如何运作的。 你探究了幕后并改动了一点 Vue.js 源码。 你学习了一些 Vue 的关键概念， 也尝试了几种安装 Vue 的方法。 我们已经启动了项目； 我们将继续开发完善。 我们也知道了如何调试测试我们的方程式。

在第一章， 我们讨论了组件甚至创建了一些。 在这章， 我们将继续使用组件并时间一些有趣的指令。 换句话说， 我们要：

* 重返组件话题回顾组件到底是什么
* 为我们的方程式创建组件
* 学习什么是单文件组件
* 学习如何用特性去达到响应式的 CSS 变换

## 回顾组件

正如你在前面章节所记得， 组件是 Vue 方程式中拥有自己作用域， 数据， 方法的特殊部分。 方程式可以使用组件并重用它。 前一章， 你知道了组件以 *Vue.extend({...})* 方法创建， 以 *Vue.component()* 语法注册。 所以为了创建使用组件我们需要这样做：

```
//创建组件
var HelloComponent = Vue.extend({
  template: '<h1>Hello</h1>'
});
//注册组件
Vue.component('hello-component', HelloComponent);
//初始化 Vue 方程式
new Vue({
  el: '#app'
});
```

我们在 **HTML** 中这样使用组件：

```
<div id='app'>
  <hello-component></hello-component>
</div>
```

### Tip
初始化及注册可以写成单个 Vue 组件的规范写法：

```
Vue.component('hello-component', {template: '<h1> Hello </h1>'});
```

### 使用组件的好处
在深入探究组件重写方程式前我们了解一些东东。 在本章， 我们将覆盖以下内容： 在组件中控制数据及 *el* 属性， 组件模板， 作用域和预处理器。

### 在 HTML 中声明模板

在我们签个例子中， 我们创建了一个用字符串重写的 Vue 组件。 它非常简单， 因为我们需要的都在其中。 现在， 想象一下我们的组件有一个更为复杂的 HTML 结构。 用字符串模板编写复杂组件容易出错， 冗余而且违反最佳实践。

### Tip

我指的最佳实践是说简洁可维护的代码。 用字符串写的复杂的 **HTML** 完全不具可维护性。

Vue 可以通过 *<template>* 标签在 **HTML** 声明模板。

在 **HTML** 中重写我们的事例。

```
<template id="hello">
  <h1>Hello</h1>
</template>
```

然后再里面加上我们的组件， 我们需要一个 ID 来指定模板。

```
Vue.component('hello-component', {
  template: '#hello'
})
```
我们的整个代码看上去像这样：

```
<body>
  <template id="hello">
    <h1>Hello</h1>
  </template>
  <div id="app">
    <hello-component></hello-component>
  </div>
  <script src="vue.js"></script>
  <script>
    Vue.component('hello-component', {
      template: '#hello'
    });
    new Vue({
      el: '#app'
    });
  </script>
</body>
```

在前面的事例里， 我们只在组件中使用了 *template* 特性。 现在让我们来看看数据和 *el* 特性应该在组件里如何配置。

## 在组件里控制数据和 el 属性
正如前面提及的， 组件的语法和 Vue 实例的语法很想， 但是它必须扩展自 Vue 而非直接被调用。 有了这个前提， 貌似这样创建组件才对：

```
var HelloComponent = Vue.extend({
  el: '#hello',
  data: {msg: 'hello'}
})
```

但是这样会导致作用域泄漏， 每一个 *HelloComponent* 实例都会共享相同的 *data 和 el*。 这不是我们想要的。  这就是 Vue 为什么会明确地要求我们以函数的形式来声明这些属性。

```
var HelloComponent = Vue.component('hello-component', {
  el: function () {
  return '#hello';
  },
  data: function () {
    return {
      msg: 'Hello'
    }
  }
});
```

甚至当你以对象的方式声明 *data* 和 *el* 时， Vue 会有善意的警告。

![](imgs/3-1.png)

Vue 会在你用对象作为数据时发出警告

## 组件的作用域

正如已经提到的， 所有的组件拥有它们自己的作用域， 而且不会被其他组件访问到。 然而， 全局的方程式作用域可以被所有注册过的组件访问到哦。
你可以看到组件的作用域是本地的， 而方程式的作用域是全局的。 这是当然得。 但是， 在组件内使用父作用域不再直接。你不得不明确指出到底哪个组件的父级数据属性可以被访问，通过使用 *prop* 属性，然后通过 *v-bind* 语法把他们绑定到组件实例上。

我们可以先声明 HelloComponent 组件并包含数据及 msg 特性：

```
Vue.component('hello-component', {
    data: function () {
      return {
        msg: 'Hello'
      }
    }
});
```

创建 Vue 实例并包含一些数据：

```
new Vue({
  el: '#app',
  data: {
    user: 'hero'
  }
});
```

在我们的 **HTML** 里， 创建模板并以 ID 的形式应用到组件上：

```
//模板声明
<template id="hello">
  <h1>{{msg}} {{user}}</h1>
</template>

//在组件中使用模板
Vue.component('hello-component', {
  template: '#hello',
  data: function () {
    return {
    msg: 'Hello'
    }
  }
});
```
为了在页面中看到组件， 我们应该在 **HTML** 中调用它：

```
<div id="app">
 <hello-component></hello-component>
</div>
```

如果你在浏览器中打开页面， 你只能看到 **Hello**； *user* 数据属性未被绑定到组件中：

![](imgs/3-2.png)

父级的数据属性未被绑定到 Vue 组件中。

为了绑定父级的数据， 我们不得不做一下两件事：

* 在 prop 特性中指明这个属性
* 把它绑定到 hello-component 调用

```
//在组件里调用父级数据特性
Vue.component('hello-component', {
  template: '#hello',
  data: function () {
    return {
      msg: 'Hello'
    }
},
  props: ['user']
});

//向组件绑定 user 数据属性
<div id="app">
<hello-component v-bind:user="user"></hello-component>
</div>
```

刷新页面， 你将看到如下消息：

![](imgs/3-3.png)

纠正向父级数据属性的绑定后， 一切按期执行了。

### Tip
实际上， *v-bind:user* 语法可以有如下的简写：

```
<hello-component :user="user"></hello-component>
```

## 组件嵌套组件

组件的美好之处在于它们可以在其它组件里面重用就像乐高中的砖块一样！ 我们来创建另一个组件； 一个叫 **greetings** 被两个二级组件(form asking 和 hello component )组成的组件。

我们先来声明模板：

```
<!--form 模板-->
<template id="form">
  <div>
    <label for="name">What's your name?</label>
    <input v-model="user" type="text" id="name">
    </div>
</template>
  //saying hello 模板
<template id="hello">
  <h1>{{msg}} {{user}}</h1>
</template>
```

现在，我们在这些模板的基础上来注册两个 Vue 组件：

```
//注册 form 组件
Vue.component('form-component', {
  template: '#form',
  props: ['user']
});
//注册 hello 组件
Vue.component('hello-component', {
  template: '#hello',
  data: function () {
  return {
    msg: 'Hello'
  }
},
  props: ['user']
});
```

最后， 我们将创建我们的 greetings 模板， 它使用了 *form 和 hello* 组件。 别忘了我们已经向组件绑定了 *user* 属性。

```
<template id="greetings">
  <div>
  <form-component :user="user"></form-component>
  <hello-component :user="user"></hello-component>
  </div>
</template>
```

此时， 我们可以创建我们的 greetings 组件并在内使用 greetings 模板。

我们在这个组件内初始化带 user 名字的数据函数：

```
//基于 greetings 模板创建 greetings 组件
Vue.component('greetings-component', {
  template: '#greetings',
  data: function () {
    return {
      user: 'hero'
    }
  }
});
```

在我们的主要方程式中， 调用 greetings 组件：

```
<div id="app">
  <greetings-component></greetings-component>
</div>
```

别忘了初始化 Vue 实例

```
new Vue({
  el: '#app'
})
```

在浏览器中打开页面， 你可以看到如下输出：

![](imgs/3-4.png)

一个由不同组件构成的页面

尝试在 input 内改变 name 值。期望它会改变因为我们已经绑定了它， 但是奇怪的是， 改变并未发生。 啊哦， 就是这样。 默认情况下， 所有的属性遵守单向数据绑定。 这意味着在父级作用域内的变化将通知到所有子极组件反之失效。 这可以防止子极组件意外破坏父级状态。 就是这样， 但是， 也可以通过调用 *events* 强迫子极组件与他们的父级通信。 具体查看 Vue 文档 https://vuejs.org/guide/components.html#Custom-Events。

在我们的例子中， 我们可以在每次输入变化时向 form input 组件绑定 user 模型， 然后分发 input 事件。 我们通过使用 *v-on:input* 修饰符来完成它， 就如在这里被描述的一样  https://vuejs.org/guide/components.html#Form-Input-Components-using-Custom-Events 。

因此， 我们必须向 form-component 传入 *v-model="user"* ：

```
<form-component v-model="user"></form-component>
```

然后， form-component 应该接收 value 属性并分发 input 事件：

```
Vue.component('form-component', {
  template: '#form',
  props: ['value'],
  methods: {
    onInput: function (event) {
      this.$emit('input', event.target.value)
    }
  }
});
```

在 form-component 模板内的输入框应该绑定 *v-on:input* 和用 *v-on:input* 修饰符的 onInput 方法：

```
<input v-bind:value="value" type="text" id="name" v-on:input="onInput">
```

### Tip

事实上， 在先前的 Vue 2.0 中，这种在组件和父级间双向的同步是可以通过 **sync** 修饰符来交流属性的：

```
<form-component :user.sync="user"></form-component>
```

刷新页面。 你现在就可以改变输入框内的值了， 它会迅速地传递给父级作用域， 从而也也可通知给其它子组件。

![](imgs/3-5.png)

通过 .sync 修饰符可以在父级和子组件键形成双向绑定。

你可以在这里发现这个事例 https://jsfiddle.net/chudaol/1mzzo8yn/。

### Tip

在 Vue 2.0 版本前， 这里还有其它数据绑定修饰符， *.once* 。用这个修饰符， 数据将只绑定一次， 任何其它的变化不再影响到组件的状态。 比较下面几种方式：

```
<form-component :user="user"></form-component>

<form-component :user.sync="user"></form-component>

<form-component :user.once="user"></form-component>
```
