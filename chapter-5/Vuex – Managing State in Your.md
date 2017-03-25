# Vuex – Managing State in Your Application

在前面的章节里， 你学习了 Vue.js 中最重要的概念之一： 数据绑定。 你在我们的方程式中学习并应用一堆绑定数据的方法。 你也学习了怎样去使用指令， 监听事件， 创建并调用方法。 在本章， 你将看到如何去管理呈现方程式全局状态的数据。 我们来聊聊 Vuex, Vuex 是一个专为 Vue.js 方程式开发的状态管理模式。 你将学习如何创建一个全局的数据仓库并如何在组件内检索变化。 我们将定义一个将在方程式中的本地数据， 我们将使用 Vuex 仓库与全局状态合作。

总结起来就是：
* 理解本地和全局状态间的区别
* 理解 Vuex 到底是什么和它的工作机制
* 学习在全局仓库中使用数据
* 学习仓库的 getters, mutations, actions
* 在购物清单和番茄钟方程式中安装使用 Vuex 仓库

## 父子组件间交流， 事件及脑筋急转弯

记得我们的购物清单方程式吗？ 记得我们的 *ChangeTitleComponent* 组件吗， 我们是如何确保在子组件内的输入影响到父组间的标题的？ 你记得每个组件都拥有它自己的作用域， 父级组件的作用域不能被子极组件影响。 因此， 为了从子极组件内传递给父级组件， 我们使用了事件。 你需要用父级组件上 *v-on* 指令调用子组件的 $emit 方法和被分发的事件名称。

如果是原生事件例如 *input*, 会更简单。 仅仅绑定需要的特性作为 *v-model* 到子极组件, 然后在子组件内通过 *$emit* 方法调用事件的名称。

事实上， 这就是我们在 *ChangeTitleComponent* 组件上做的全部。

打开[ chapter5/shopping-list](https://github.com/PacktPublishing/Learning-Vuejs-2/tree/master/chapter5/shopping-list) 文件夹。

你在 *shoppinglistComponent* 组件模板内使用 *v-model* 指令来给 *ChangeTitleComponent* 绑定标题。

```
//ShoppingListComponent.vue
<template>
  <div>
    <...>
    <div class="footer">
      <hr />
      <change-title-component v-model="title"></change-title-component>
    </div>
  </div>
</template>
```

然后， 我们在 *ChangeTitleComponent* 组件内 *props* 特性内声明标题的模型值， 在输入动作上分发 *input* 事件：

```
<template>
  <div>
    <em>Change the title of your shopping list here</em>
    <input :value="value" @input="onInput"/>
  </div>
</template>
<script>
  export default {props: ['value'],
    methods: {
      onInput (event) {
        this.$emit('input', event.target.value)
      }
    }
  }
</script>
```

很直接， 对吗？

如果我们尝试在输入框内改变标题， 我们的的购物清单会相应地改变：

![](imgs/5-1.png)

在父级与子极组件间建立了基于事件交流后， 我们能改变标题

看起来我们已经实现了我们的目标。 但是， 如果你打开开发者工具， 你将看到显眼的错误：

```
[Vue warn]: Avoid mutating a prop directly since the value will be overwritten
whenever the parent component rerenders. Instead, use a data or computed
property based on the prop's value. Prop being mutated: "title"
```

哎哟! Vue 实际上是对的， 我们正在改变包含在 *ShoppingListComponent* 组件内的 *props* 特性数据。 这个特性来自主要的父级组件 -- *App.vue*, 相对的， 我们 *ShoppingListComponent* 组件的父级组件。 我们已经知道了我们不能改变来自子组件的父级数据。 如果标题直接属于 *ShoppingListComponent*, 一切没有问题， 但在这个例子中， 我们这样定义会出错。

你应该注意到了， 你可能注意到了这里已经没有更多地方来包含一片不改变的数据了。 看看选项卡的标题。 它一直在显示 **Groceries**。 但我们也想改变它。

小提示： 我已经增加了一个新的组件， *ShoppingListTitleComponent* 。 它来呈现选项卡的标题。 你还记得计算属性吗？ 注意这个组件就包含一个添加 *#* 给 ID 输入， 它通过 *props* 特性生成锚点：

```
<template>
  <a :href="href" :aria-controls="id" role="tab" data-toggle="tab">
  {{ title }}</a>
</template>
<script>
  export default{
  props: ['id', 'title'],
  computed: {
    href () {
      return '#' + this.id
    }
  }
  }
</script>
```
