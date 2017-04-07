## Testing – Time to Test What We Have Done So Far!

在前一章， 你已经学习了如何创建使用插件。 我们使用了已有的 *resource* 插件， 创建了自己的 *NoiseGenerator* 插件。

在本章， 我们将保证我们的购物清单和番茄钟方程式的质量。 我们要用不同的测试技术来测试这些方程式。 首先， 我们将对 Vue 组件和 Vuex-related 上的 actions ,  mutations, getters 进行单元测试。 然后， 我们将学习如何用 Nightwatch 来完成端对端测试。 所以呢， 在本章， 我们要做以下几件事：

* 谈谈单元测试和端对端测试的重要性
* 对购物清单和番茄钟进行单元测试
* 学习在单元测试中如何去模拟服务器响应
* 用 Nightwatch 对两个方程式进行端对端测试

## 为啥要单元测试

在写单元测试前， 我们来说说我们为啥要写单元测试呢？ 单元测试为什么很重要？ 有时在我写代码时我只想着我的代码测试覆盖率； 我想到达百分百的水平。

代码覆盖率是一项非常重要的指标， 它帮助我们理解代码流程和哪些是需要被测试的。 但是它不保证单元测试的质量。 你可以让你的代码拥有百分百的覆盖率通过测试你所有的函数， 但是如果你的断言写错了， 代码也有可能是错的。 写好单元测试需要像是搞艺术， 需要时间和耐心。 但当你的单元测试足够棒， 集中精神写一些良好的断言时， 将会由以下好处：

* 帮助我们识别算法和逻辑上的错误
* 帮助我们提升代码质量
* 让我们更易写出易于测试的代码
* 防止未来的变化破环我们的功能
* 帮助我们控制工期

易于测试的代码也易于阅读。 易于阅读的代码更加健壮可维护性也更高。 可维护性是方程式质量的标准之一。

## 注意

在 https://chudaol.github.io/presentation-unit-testing/#/ 查看更多单元测试信息。

我们来为我们的方程式写点单元测试。

我们使用 Karma 测试器， Mocha 测试框架， Chai 断言库， Sinon 模拟库。

更多相关信息， 看下表：

* Karma: http://karma-runner.github.io/
* Mocha: https://mochajs.org
* Chaijs: http://chaijs.com/
* Sinon: http://sinonjs.org/

如果你没有使用 *vue-cli webpack* 脚手架启动我们的方程式， 我们通过 *npm* 来安装所有的这些工具。 但是在本例中， 我们不需要安装。 查看你的 *package.json* 文件：

```
"devDependencies": {
  <...>
  "chai": "^3.5.0",
  <...>
  "karma": "^0.13.15",
  "karma-chrome-launcher": "^2.0.0",
  "karma-coverage": "^0.5.5",
  "karma-mocha": "^0.2.2",
  "karma-phantomjs-launcher": "^1.0.0",
  "karma-sinon-chai": "^1.2.0",
  "mocha": "^2.4.5",
  <...>
}
```  

你当然知道为简单的方程式写单元测试有多么简单。 这就像说说话一样。 函数应该返回 X 如果输入为 Y。 我期望它返回 X。

所以如果我们导出一个模块， 就是说， 这个函数返回两个参数的和， 这个函数的单元测试必须以两个不同的参数调用并返回一些期望值。 所以， 假设我们有这样一个函数：

```
function sum(a, b) {
  return a + b
}
```

然后我们的单元测试可能像这样：

```
it('should follow commutative law', () => {
  let a = 2;
  let b = 3;
  expect(sum(a, b)).to.equal(5);
  expect(sum(b, a)).to.equal(5);
})
```

我们不应为我们测试我们认为可能的值时感到愧疚。 空输入， 负输入， 字符串输入都一样！ 你知道这条 tweet( https://twitter.com/sempf/status/514473420277694465 ) 吗？

![](imgs/7-1.png)

关于 QA 工程师状态的毒推

想想所有可能的输入和适当的输出。 在期望和断言中进行表达。 运行测试。 看看会发生什么错误。 修复代码。

## 为 Vue 方程式进行单元测试

首先， 我们来查看一些 Vue 方程式和组件的单元测试细节。 为了给组件实例写测试， 首先， 来实例化！ 很对吧？ 情况是， 我们怎样来实例化 Vue 组件来让它的方法变得既可理解既测试？ 为了测试组件的初始状态的基本断言， 你必须导入它们并断言它们的属性。 如果你想测试动态的属性--那些改变被绑定给 DOM 后的组件的属性。 你必须这样做：

1. 导入组件
2. 通过把它传入 Vue 方程式来实例化它
3. Mount

## Tip

当实例被绑定到实体 DOM 时， 一旦实例化， 就会马上编译。 在我们的事例中， 我们不会绑定任何实例给实体 DOM 元素， 因此我们必须通过手动调用 mount 方法($mount) 显示地编译它。

现在你可以使用创建好的实例并访问它的方法。 在伪代码内， 看起来就像这样：

```
import MyComponent from <path to my component>
var vm = new Vue(MyComponent).$mount()
```

现在我们可以访问所有的 *vm* 实例方法并测试。 剩下的像 *data, props* 等等我们都可以模拟。 创建假数据没问题， 因为这让我们有可能去试着简单地去挑选输入并为每个输出测试。

如果你想拥有更真实的场景来测试， 你可以使用 *ref* 特性来为 *Vue* 构建组件。 这个 Vue 实例， 以一种普通的方式实例了仓库和数据， 并绑定数据项给组件。 然后， 你就可以通过 *$refs* 来访问组件实例。 这种绑定看起来像这样：

```
import store from <path to store>
import MyComponent from <path to my component>

// load the component with a vue instance

var vm = new Vue({
template: '<div><test :items="items" :id="id" ref=testcomponent></test></div>',
components: {
  'test': MyComponent
  },
  data() {
    return {
      items: [],
      id: 'myId'
    }
  },
  store
}).$mount();
var myComponent = vm.$refs.testcomponent;
```

现在你可以测试 *myComponent* 的所有方法也不必担心重写它的 *props, methods* 及其他实例相关的东东。 这很好办到； 但是， 如你看到的， 这不是最简单的设置， 你得考虑所有事。 例如， 如果你的组件调用了一些仓库 action， 这个 action 会调用一些 API 的方法， 你应该准备好模拟服务器响应。

我个人喜欢让一切变得更简单， 模拟所有的数据输入， 集中精力在测试函数可能的输出和所有的临界情况上。 但是， 这只是我个人的观点， 我们该多尝试。 在本章， 我们将尝试不同的实现手段。

## 为我们的购物清单方程式写单元测试

在开始写单元测试前， 我们来建立一些规则。 对于我们每个 *.js* 和 *.vue* 文件， 都应该有相对应的测试文件， 它们将拥有相同的名字和一个 *.spec.js* 拓展名。 这些说明遵循下面说明：

* 描述我们将要测试的文件
* 对每个被测试的方法有一个 *describe* 方法
* 对每个描述的事例有一个 *it* 方法

所以， 如果我们有一个 *myBeautifulThing.js* 文件及其说明， 可能是这样的：

```
// myBeautifulThing.js
export myBeautifulMethod1() {
  return 'hello beauty'
}

export myBeautifulMethod2() {
  return 'hello again'
}

// myBeautifulThing.spec.js
import myBeautifulThing from <path to myBeautifulThing>

describe('myBeautifulThing', () => {
//define needed variables

describe('myBeautifulMethod1', () => {
  it('should return hello beauty', () {
    expect(myBeautifulThing.myBeautifulMethod1()).to.equal('hello beauty')
  })
})
})
```

我们以用测试转换所有在 *vuex* 文件夹内的东东为起点。

## 测试 actions, getters, mutations

在这部分， 使用[ chapter7/shopping-list](https://github.com/PacktPublishing/Learning-Vuejs-2/tree/master/chapter7/shopping-list) 这里的代码。 别忘了运行 *npm install* 。 注意这里有两个新的 muattions： ADD_SHOPPING_LIST 和 DELETE_SHOPPING_LIST。 这些 mutations 添加新的购物列表给清单并通过它的 ID 来移除。 它们在 promise 失败处理器内的 *createShoppingList* 和 *deleteShoppingList* actions 上：

```
//actions.js
createShoppingList: (store, shoppinglist) => {
  api.addNewShoppingList(shoppinglist).then(() => {
    store.dispatch('populateShoppingLists')
  }, () => {
    store.commit(ADD_SHOPPING_LIST, shoppinglist)
  })
},

deleteShoppingList: (store, id) => {
  api.deleteShoppingList(id).then(() => {
    store.dispatch('populateShoppingLists')
  }, () => {
    store.commit(DELETE_SHOPPING_LIST, id)
  })
}
```  

因此， 即使我们的后端服务挂了， 功能依然能正常运行。

如果查看你的项目结构， 你将看到这里已经有了一个叫 *test* 的目录啦。 其中有两个目录， *unit* 和 *e2e*。 现在呢， 我们该进入 *unit* 目录。 在这， 你将看到另一个叫 *specs* 的目录。 我们所有的单元测试说明都在这儿。 我们在 *specs* 内先创建一个 *vuex* 目录。 这里存放我们所有的 Vuex 相关文件。

我们以测试 *mutations.js* 方法为起始点。

创建 *mutations.spec.js* 文件。 在这个文件里， 我们应该导入 *mutations.js* 和 mutations 类型， 这样我就可以简单地调用 mutations 了。 看下在 *mutations.js* 内的 mutations 声明。 所有的 mutations 接收 *state* 和一些其它的参数。 我们也来创建一个假的拥有 *shoppinglist* 数组的 *state* 对象， 这样我们就能在我们的测试里使用它了。

在为每个测试前我们重置它为空数组。

所以呢， 准备工作后， 启动后的 spec 看起来是这样：

```
// mutations.spec.js
import mutations from 'src/vuex/mutations'
import { ADD_SHOPPING_LIST, DELETE_SHOPPING_LIST, POPULATE_SHOPPING_LISTS, CHANGE_TITLE } from 'src/vuex/mutation_types'

describe('mutations.js', () => {var state
beforeEach(() => {
  state = {
    shoppinglists: []
  }
})
})

```

现在为 ADD_SHOPPING_LIST mutation 添加一些测试。

再次检查它：

```
[types.ADD_SHOPPING_LIST] (state, newList) {
  state.shoppinglists.push(newList)
},
```

这个 mutation 仅仅添加刚接收到的对象给 *shoppinglists* 数组。 非常直接易于测试。

以创建一个有名字的 *describe* 语句开始：

```
describe('ADD_SHOPPING_LIST', () => {
})
```

现在， 在这个 *describe* 回掉里， 我们可以添加一些需要的断言。 让我们来想想当我们添加一个新的购物清单给购物清单数组后悔发生什么呢？ 首先， 数组的长度会增加， 里面当然有刚添加的购物清单对象啦。 这是最基本需要测试的。 我们的 *it* 函数看起来像是这样：

```
it('should add item to the shopping list array and increase its
length', () => {

//call the add_shopping_list mutations
mutations[ADD_SHOPPING_LIST](state, {id: '1'})

//check that the array now equals array with new object
expect(state.shoppinglists).to.eql([{id: '1'}])

//check that array's length had increased
expect(state.shoppinglists).to.have.length(1)
})
```

创建完这个函数后， 整个 spec 代码看起来是这样的:

```
// mutations.spec.js
import mutations from 'src/vuex/mutations'
import { ADD_SHOPPING_LIST, DELETE_SHOPPING_LIST, POPULATE_SHOPPING_LISTS,
CHANGE_TITLE } from 'src/vuex/mutation_types'

describe('mutations.js', () => {
  var state
  beforeEach(() => {
    state = {shoppinglists: []
    }
  })

  describe('ADD_SHOPPING_LIST', () => {
    it('should add item to the shopping list array and increase its
    length', () => {
      mutations[ADD_SHOPPING_LIST](state, {id: '1'})
      expect(state.shoppinglists).to.eql([{id: '1'}])
      expect(state.shoppinglists).to.have.length(1)
    })
  })
})
```

我们来运行测试！ 在项目目录内打开开发者工具运行：

**npm run unit**

你应该看到如下的输出：

![](imgs/7-2.png)

运行测试后的输出

记得那个关于 QA 工程师的玩笑吗？ 我们可以为 *add_shopping_list* 函数测试所有可能的输入。 会发生什么呢， 例如， 如果我们一个对象也没传入呢？ 这样， 这里应该不会添加任何购物清单给清单数组， 对吧？ 我们来试一下。 创建一个新的 *it* 语句， 试着不包含第二个参数来调用函数。 为一个空数组断言。

这个测试看起来就像这样：

```
it('should not add the item if item is empty', () => {
  mutations[ADD_SHOPPING_LIST](state)
  expect(state.shoppinglists).to.have.length(0)
})
```

以 *npm run unit* 命令开始测试。 哇哦！ 失败了！ 错误如下：

```
expected [ undefined ] to have a length of 0 but got 1
```

为啥呢？ 查看对应的 mutation。 它把刚接收到的参数传给了数组。 这就是我们可以蒜煸往里面加东西的原因！ 你还记得我前面说的良好的测试可以帮助我们创建更少错误的代码吗？ 这个例子就是。 现在我们意识到我们应该在添加新项时应该添加一些检查了。 我们就来添加一些检查接收的选项是对象的检查。 打开 ADD_SHOPPING_LIST mutations 重写：

```
//mutations.js
[types.ADD_SHOPPING_LIST](state, newList) {
  if (_.isObject(newList)) {
    state.shoppinglists.push(newList)
  }
}
```

现在运行测试。 通过了！

当然， 我们可以有更精确的检查。 我们可以检查空对象， 并运行一些验证如检查这个对象是否包含 *id, items, title* 等。 我把它当成一个小练习留给你。 试着想出所有可能输入的值及输出的值， 写下所有可能的断言。

## 良好的测试条件
