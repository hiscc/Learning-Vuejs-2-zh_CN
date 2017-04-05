# Plugins – Building Your House with Your Own Bricks

在前面的章节里， 你已经学习了如何用 Vuex 架构来管理全局方程式仓库。 你学习了一堆新概念并应用了它们。 你也学习了如何去创建一个仓库， 如何定义它的状态和 mutations， 如何使用 actions 和 getters。 我们们学会的知识再次激活了我们的购物清单和番茄钟方程式。

在本章， 我们将回顾 Vue 插件， 看看它们是如何工作的， 为什么必须要创建插件。 我们将使用一些已有的插件并自己再创建一个。

总结起来呢， 就是一下几点：

* 理解 Vue 插件的本质
* 在购物清单方程式里使用 resource 插件
* 创建一个生成白， 粉， 棕的插件并在番茄钟内应用

## Vue 插件的本质

Vue 中的插件完全和用在任何作用域的插件一样： 添加功能， 也基于插件的本质， 去完成系统中一些核心不能完成的功能。 Vue 的插件可以提供不同的功能， 像定义一些 Vue 全局的方法甚至是实例方法， 提供新的指令， 过滤器，变换。

为了使用那些已有在插件， 首先你必须安装它：

**npm install some-plugin --save-dev**

然后， 告诉 Vue 你要在方程式内使用它：

```
var Vue = require('vue')
var SomePlugin = require('some-plugin')
```

**Vue.use(SomePlugin)**

我们也可以创建我们自己的插件。 很简单的。 你的插件必须提供一个 *install* 方法来定义一些或全局或实例的方法， 或者是一些自定义指令：

```
MyPlugin.install = function (Vue, options) {
  // 1. add global method or property
  Vue.myGlobalMethod = ...
  // 2. add a global asset
  Vue.directive('my-directive', {})
  // 3. add an instance method
  Vue.prototype.$myMethod = ...
}
```

然后就可以像使用其它插件一样使用了。 在本章， 我们将使用已有的 *resource* 插件并创建我们自己的插件。

## 在我们的购物清单方程式内使用 vue-resource 插件

打开我们的[购物清单方程式](https://github.com/PacktPublishing/Learning-Vuejs-2/tree/master/chapter6/shopping-list) 并运行 *npm install npm run dev*。 很棒， 但它依旧使用硬编码来建立购物清单。 如果我们想增删改查一些条目时使用 *resource* 插件会很方便， 这个插件允许我们创建简单的 REST 资源并调用 REST 方法。 在开始前， 我们来总结一下我们将需要做的工作：

* 首先， 我们需要一个简单的服务器来储存我们的购物清单。 后端提供所有需要的功能。
* 在创建完成服务及所有后端工作后， 我应该安装 *vue-resource* 插件来创建资源和行为来供后端调用。
* 为了保证数据的完整性， 我们应该在每次服务状态更新时执行一系列行为。
* 在方程式开始时， 我们应该从后端取得所有的购物清单数据并分配给我们的仓库。
* 我们应该提供一个机制来创建新的购物清单或删除已有的购物清单

听起来不难吧？ 开动起来！

## 创建一个简单的服务

为了足够简单， 我们将使用非常基本易用的 HTTP 服务， 用 JSON 文件来储存数据。 这个服务是 *json-server* 在https://github.com/typicode/json-server 。 把它安装在我们的方程式目录里：

**cd shopping-list**
**npm install --save-dev json-server**


创建一个叫 *db.json* 的服务器文件夹， 内容如下：

```
//shopping-list/server/db.json

{
  "shoppinglists": [
  ]
}
```

这将是我们的数据库。 我们在我们的 *package.json* 内添加一些脚本， 从而更容易地开始我们的服务：

```
"scripts": {
  "dev": "node build/dev-server.js ",
  "server": "node_modules/json-server/bin/index.js --watch
             server/db.json",
  <...>
},
```

现在， 启动服务， 按如下运行：

**cd shopping-list**
**npm run server**

在 http://localhost:3000/shoppinglists 打开浏览器。 你将看到一个空数组。 这是因为我们的数据库依然是空的。 我们试着用 curl 来添加一些数据：

**curl -H "Content-Type:application/json" -d '{"title":"new","items":[]}'**
**http://localhost:3000/shoppinglists**

如果你现在刷新页面， 你将看到新添加的值。

既然我们有了自己的 REST 服务。 那我们就在 *vue-resource* 的帮助下使用它吧！


## 安装 vue-resource, 创建 resource 和它的方法

在深入学习 *vue-reource* 之前， 查看它的文档  https://github.com/vuejs/vue-resource/blob/master/docs/resource.md。 文档提供了基于 URL 来简单创建 resources 的方法。 在 resource 被创建后， 我们可以对他进行 *get， delete, post update*。

在项目的文件夹内安装：

**cd shopping-list**
**npm install vue-resource --save-dev**

现在我们来为我们的端口创建 API 。 在 *src* 文件夹内， 创建一个叫 *api* 的二级文件夹。 在里面创建 *index.js* 文件， 导入 *vue-resource* 插件并告诉 *Vue* 来使用它：

```
//api/index.js
import Vue from 'vue'
import VueResource from 'vue-resource'

Vue.use(VueResource)
```

好的！现在我们来创建 *ShoppingListResource* 并在里面添加一些方法。 用 *vue-resource* 插件创建 resource , 我们只需要在 *Vue* 上调用 *resource* 方法并传入 URL 即可。

```
const ShoppingListsResource = Vue.resource('http://localhost:3000/' + 'shoppinglists{/id}')
```

*ShoppingListsResource* 常量现在暴露了我们所有需要的方法(CRUD)。  通过简单地暴露 resource 我们就可以完成。 但是我们需要为每个操作导出相应的方法：

```
export default {
  fetchShoppingLists: () => {
    return ShoppingListsResource.get()
  },
  addNewShoppingList: (data) => {
    return ShoppingListsResource.save(data)
  },
  updateShoppingList: (data) => {
    return ShoppingListsResource.update({ id: data.id }, data)
  },
  deleteShoppingList: (id) => {
    return ShoppingListsResource.remove({ id: id })
  }
}
```

*api/index.js* 完整的代码在 https://gist.github.com/chudaol/d5176b88ba2c5799c0b7b0dd33ac0426 。

就是这样！ 我们的 API 已经准备好了接收 Vue 的数据了！

## 从获取所有购物清单的数据开始

从创建一个获取 *shoppinglists* 状态的行为开始。 在它创建后， 我们在 *App.vue* 上调用。

在 *mutations_types.js* 内定义一个常量：

```
//mutation_types.js
export const POPULATE_SHOPPING_LISTS = 'POPULATE_SHOPPING_LISTS'
```

现在创建一个 mutation 。 这个 mutation 将只接收购物清单数组并分配给 *shoppinglists* 状态：

```
//mutations.js
export default {
  [types.CHANGE_TITLE] (state, data) {
    findById(state, data.id).title = data.title
  },
  [types.POPULATE_SHOPPING_LISTS] (state, lists) {
    state.shoppinglists = lists
  }
}
```

好的！ 现在我们只需要一个使用 API 方法来分发 mutation 的 action 了。 在 *actions.js* 文件内导入 API并创建相应的 action 方法：

```
import { CHANGE_TITLE, POPULATE_SHOPPING_LISTS } from './mutation_types'
import api from '../api'

export default {
  changeTitle: ({ commit }, data) => {
    commit(CHANGE_TITLE, data)
  },
  populateShoppingLists: ({ commit }) => {
    api.fetchShoppingLists().then(response => {
      commit(POPULATE_SHOPPING_LISTS, response.data)
    })
  }
}
```

在前面的代码里， 我们完成了一个简单的任务 -- 我们调用了 *fetchShoppingLists* API 方法， 反过来， 调用了 resource 的 *get* 方法。 这个方法产生一个 *http GET* 请求并当数据返回时完成一个 promise 。

数据将被用于分发填充 mutation 。 这个方法将为仓库状态属性 *shoppinglists* 分配数据。 这个属性是响应式的；  你还记得吗？
这意味着所有依赖 *shoppinglists* 属性的视图将被更新。 我们现在在 *App.vue* 组件内的 mounted 状态上使用 action。 更多关于 mounted 状态的钩子信息 https://vuejs.org/v2/api/#mounted 。

打开 *App.vue* 组件， 导入 *mapActions* 对象， 在组件的 *methods* 属性上映射 *populateShoppingLists* action， 在 *mounted* 控制器上调用。 所以我们的 *App.vue* 将会是这样的：

```
<script>
  import ShoppingListComponent from './components/ShoppingListComponent'
  import ShoppingListTitleComponent from
  './components/ShoppingListTitleComponent'
  import store from './vuex/store'
  import { mapGetters, mapActions } from 'vuex'

  export default {
    components: {
      ShoppingListComponent,
      ShoppingListTitleComponent
    },
    computed: mapGetters({
      shoppinglists: 'getLists'
    }),
    methods: mapActions(['populateShoppingLists']),
    store,
    mounted () {
      this.populateShoppingLists()
    }
  }
</script>
```

如果你现在打开页面， 会发现这里只有一个我们用 *curl* 创建的购物清单， 就像下图一样：

![](imgs/6-1.png)

这里展示了由简单服务创建的购物清单！

试着用 *curl* 来插入更多的数据！

## 在变化上更新服务器状态

非常棒， 我们现在有了 REST API 服务， 一切正常运行。 试着添加一些购物清单或改变标题。 刷新页面。 哇哦， 列表空了！ 没错， 我们有一个 API 方法来更新给定的购物清单， 但是数据没有同步， 所以我们的服务器没有意识到变化。

我们来定义一个组件来让服务器接收更改。 下面的三件事将是我们要做的：

* 列表的标题可以在 *changeTitleComponent* 内改变
* 新的列表项可以在 *AddItemComponent* 内添加
* 购物清单的列表项可以在 *ItemComponent* 内切换

我们必须创建一个行为来触发所有的更改。 在这个行为内， 我们应该调用更新 API 的方法。 在 *api/index.js* 内查看 更新方法； 它必须接收整个购物清单对象为参数：

```
//api/index.js
updateShoppingList: (data) => {
  return ShoppingListsResource.update({ id: data.id }, data)
}
```

我们来创建一个行为来接收参数为 id ，通过 ID 来取回购物列表， 调用 API 方法。 在这么做之前，在 *getters.js* 内创建一个 *getListById* 方法并在 actions 内导入它：

```
//getters.js
import _ from 'underscore'
  export default {
    getLists: state => state.shoppinglists,
    getListById: (state, id) => {
      return  _.findWhere(state.shoppinglists, { id: id })
    }
}

//actions.js
import getters from './getters'
```

现在， 我们来调用更新购物清单的行为：

```
//actions.js
<...>

export default {
  <...>
  updateList: (store, id) => {
    let shoppingList = getters.getListById(store.state, id)
    api.updateShoppingList(shoppingList)
  }
}
```

事实上， 我们可以在 *mutations.js* 文件内删除 *findById* 方法了， 在 *getters.js* 内重用这个方法：

```
//mutations.js
import * as types from './mutation_types'
import getters from './getters'

export default {
  [types.CHANGE_TITLE] (state, data) {
    getters.getListById(state, data.id).title = data.title
  },
  [types.POPULATE_SHOPPING_LISTS] (state, lists) {
    state.shoppinglists = lists
  }
}
```

好的， 现在我们已经为 API 定义了调用 *updateList* 方法的行为了。 现在我们只需在组件内的每次变化时调用这个行为就可以了。

我们先修改 *AddItemComponent*。 我们必须在这里组件内用 *this.$store.dispatch* 方法分发 *updateList* 行为。 但是这里有个小问题 -- 我们必须把列表的 ID 传给 *updateList* 方法， 而且我们不能在这里组件里有它的引用。 但是呢， 这其实很好解决。 仅仅在组件的 *props* 添加 ID 并绑定给在组件上的调用行为。 我们的 *AddItemComponent* 组件看起来像这样：

```
//AddItemComponent.vue
<script>
  export default {
    props: ['id'],
    data () {
      return {
        newItem: ''
      }
    },
    methods: {
      addItem () {
        var text
        text = this.newItem.trim()
        if (text) {
          this.$emit('add', this.newItem)
          this.newItem = ''
          this.$store.dispatch('updateList', this.id)
        }
    }}
  }
</script>
```

在 *ShoppingListComponent* 内， 在 *add-item-component* 调用上绑定 ID：

```
//ShoppingListComponent.vue
<template>
  <...>
    <add-item-component :id="id" @add="addItem"></add-item-component>
  <...>
</template>
```

现在， 如果你试着添加列表项并刷新页面会发现添加的列表项还在！

现在， 我们应该给 *ChangeTitleComponent* 做相同的事。 打开 *ChangeTitleComponent.vue* 文件， 查看代码。 在 *changeTitle* action 上调用：

```
//ChangeTitleComponent.vue
<template>
  <div>
    <em>Change the title of your shopping list here</em>
    <input :value="title" @input="onInput({ title:
    $event.target.value, id: id })"/>
  </div>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: ['title', 'id'],
  methods: mapActions({
    onInput: 'changeTitle'
  })
}
</script>
```

我们当然可以导入 *updateList* action 并在调用 *changeTitle* action 后调用。 但是， 在 action 内部操作会更简单。 你可能记得为了分发仓库的 action , 我们应该在仓库上以 action 的名字为参数调用 *dispatch* 方法。 所以我们在 *changeTitle* action 这么修改：

```
//actions.js
export default {
  changeTitle: (store, data) => {
    store.commit(CHANGE_TITLE, data)
    store.dispatch('updateList', data.id)
  },
  <...>
}
```

完成了， 打开页面， 修改标题， 刷新页面， 标题应该不变！

我们最后需要修改的是列表项的切换属性。 我们来看看 *ItemComponent* 并决定在哪里调用 *updateList* action。

我们从未 *props* 特性添加 ID 开始， 就像我们在 *AddItemComponent* 内做的一样：

```
//ItemComponent.vue
<script>
  export default {
    props: ['item', 'id']
  }
</script>
```

我们必须给组件的调用绑定 id 属性， 在 *ItemsComponent* 内完成：

```
//ItemsComponent.vue
<template>
  <ul>
    <item-component v-for="item in items" :item="item" :id="id">
    </item-component>
  </ul>
</template>

<script>
import ItemComponent from './ItemComponent'

export default {
  components: {
    ItemComponent
  },
  props: ['items', 'id']
}
</script>
```

这意味着我们必须为 *item-component* 绑定 id 属性：

```
//ShoppingListComponent.vue
<template>
  <...>
    <items-component :items="items" :id="id"></items-component>
  <...>
</template>
```

我们应该在 *ItemComponent* 导入 *mapActions* 对象并在 *methods* 属性上导出 *updateList* 方法：

```
//ItemComponent.vue
<script>
  import { mapActions } from 'vuex'
  export default {
    props: ['item', 'id'],
    methods: mapActions(['updateList'])
  }
</script>
```

很好， 万事俱备； 我们能仅需要在 *ItemComponent* 内找到调用 *updateList* 行为的地方就可以了。

这不是一个简单的任务， 因为不像在其它组件内我们有事件控制器来处理变更并调用相应的函数。 在这， 我们只有类和模型绑定给复选框元素。 幸运的是， Vue 提供了一个 *watch* 选项来为我们附加监视器到任意的组件数据上， 而且可以给它们绑定处理器。 在我们的例子中， 我们想监听 *item.checked* 属性并调用行为。 所以， 仅仅在组件上添加一个 *watch* 特性：

```
//ItemComponent.vue
<script>
  import { mapActions } from 'vuex'
  export default {
    props: ['item', 'id'],
    methods: mapActions(['updateList']),
    watch: {
      'item.checked': function () {
      this.updateList(this.id)
      }
    }
  }
</script>
```

我们完成了！ 试着添加清单列表， 切换， 刷新页面， 什么都不变！

## 创建一个新的购物清单
