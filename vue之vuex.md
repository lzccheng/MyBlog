##### 介绍：
 Vuex是一个专门为vue应用而生的状态管理模式，类似于flux、redux。目的是为了解决组件间数据状态的交互，其实质就是一个js对象。同时还能对数据进行有效的全局单例模式管理。
 ##### store：
 1、Vuex中的store是响应式的，当其发生改变时，组件中使用到store的数据也会相应的进行更新。
 2、不能直接对store进行操作，得通过唯一的途径（commit）mutation改变store中的状态。
 首先需要实例化一个Store，同时需注入到vuex中：
 ```js
 //store.js
 import Vue form 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)  //将vuex注入到vue中
const store = new Vuex.Store({
	state: {
		count: 1
	}
})
export default store
//main.js
import Vue from 'vue'
import store from './store'
new Vue({
	el: '#app',
	store
})
```
##### store.state:
完成之后即可在组件中查看到store并在computed属性中对数据进行监听，vuex还提供了mapState函数可以store中的state快速获取：
```js
//home.vue
export default {
	created() {
		console.log(this.$store.state)
	},
	computed: {
		count() {
			return this.$store.state.count
		}
	}
}
//homve.vue
import { mapState } from 'vuex'
export default {
	created() {
		console.log(this.$store.state)
	},
	computed: {
		...mapState(['count']),
		...mapState({
			fnCount: state => state.count,  // 箭头函数可使代码更简练
			myCount: 'count',  // 传字符串参数 'count' 等同于 `state => state.count`
			fn5Count: function(state){  // 为了能够使用 `this` 获取局部状态，必须使用常规函数
				return this.myAge + state.count
			}
		})
	}
}
```
##### store.getters:
有时需要对state中的数据进行处理（例如过滤、筛选等）需要些相同的逻辑函数，而在每个组件中引入处理函数又显得过于冗余，getters就派上用场了。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
Getter 接受 state 作为其第一个参数，第二个参数为整个getters对象，可通过返回一个函数进行传参：
```js
//store.js
import Vuex from 'vuex'
const store = new Vuex.Store({
	state: {
		arr: ['a', 'b', 'c', 'd']
	},
	getters: {
		findState: (state, getters） => （id）=>  state.arr.find(i => i === id)
	}
})
export default store
//home.vue
export default {
	created(){
		console.log(this.$store.getters.findState('c'))  //'c'
	}
}
```
辅助函数：mapGetters（用法类似于mapState）
```js
import { mapGetters } from 'vuex'
export default {
	computed: {
		...mapGetters(['findState']),
		...mapGetters({
			findState2: 'findState'  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
		})
	}
}
```
##### store.mutations:
更改store中的状态的唯一方法是mutations，接受state作为第一个参数，需要store.commit方法调用，第二个参数为可传
```js
//store.js
const store = new Vuex.Store({
	state: {
		count: 1
	},
	mutations: {
		plus(state, param){
			state += param.n
		}
	}
})
//home.vue
this.$store.commit('plus', { n: 20 })
//另一种调用风格
this.$store.commit({
	type: 'plus',
	n: 20
})
```
mutations必须是同步函数，任何在回调函数中进行的状态的改变都是不可追踪的。
辅助函数mapMutations（用法与mapGetters类同），其为方法调用，写在methods中：
```js
import { mapMutations } from 'vuex'
export default {
	methods: {
		...mapMutations(['plus']),
		...mapMutations({
			add: 'plus'
		})
	}
}
```
##### store.actions:
actions类似于mutations：
1、actions提交的是mutations，而不是直接变更store的状态
2、actions可以包含任意异步操作

第一个参数是与store拥有相同的方法和属性的context对象，可用es6的参数解构提取方法，通过dispath调用actions的方法，与调用commit类同，可以在actions用dispath调用另一个actions，也可与async/await结合用于获取数据存储到state中：
```js
const store = new Vuex.Store({
	state: {
		name: 'lzccheng',
		data: ''
	},
	mutations: {
		changeName(state, param) {
			state.name = param
		},
		setData(state, data) {
			state.data = data
		}
	},
	actions: {
		async getData({ commit }) {
			return await axios.get(API).then(res => commit('setData', res.data))
		},
		
		async actonsChangeName({ commit, dispath, state }, param){
			commit('changeName', param)
			await dispath('getData')
			console.log(state.data)
		}
	}
})
//home.vue
this.$store.dispath('actionsChangeName', 'my name is lzccheng')
```
辅助函数：mapActions（与mapMutatios类似）
```js
//home.vue
import { mapActions } from 'vuex'
export default {
	methods: {
		...mapActions(['actionsChangeName']),
		...mapActions({
			change: 'actionsChangeName'
		})
	}
}
```
##### store.module:
当一个应用比较大的时候，store的状态就会变得臃肿，而module则可以将这些状态分成更小的层级，可以更好地进行管理、划分。
```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  state: {...},
  actions: {...},
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```
局部actions中想获取根的state，可在context中解构rootState值，调用根的actions或mutations可在dispath、commit中加入第三个参数{root：true}
```js
actioins: {
	someActions({ commit, dispath, state, getters, rootState, rootGetters }) {
		console.log(rootState, rootGetters )
		commit('mutations')  //调用局部的mutations
		commit('Mutations', null, { root: true })  //调用根的mutations
		dispath('Actions')   //调用局部的actions
		dispath('Actions', null, { root: true })  //调用根的actions
	}
}
```
局部getters：如果你希望使用全局 state 和 getter，rootState 和 rootGetter 会作为第三和第四参数传入 getter，也会通过 context 对象的属性传入 action。
```js
getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    }
```
module的命名空间（namespace）：
默认情况下，模块内部的 action、mutation 和 getter 是注册在全局命名空间的——这样使得多个模块能够对同一 mutation 或 action 作出响应。

如果希望你的模块具有更高的封装度和复用性，你可以通过添加 namespaced: true 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。
启用了命名空间的 getter 和 action 会收到局部化的 getter，dispatch 和 commit。换言之，你在使用模块内容（module assets）时不需要在同一模块内额外添加空间名前缀。更改 namespaced 属性后不需要修改模块内的代码。

带命名空间的绑定函数
当使用 mapState, mapGetters, mapActions 和 mapMutations 这些函数来绑定带命名空间的模块时，写起来可能比较繁琐：
```js
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
```
对于这种情况，你可以将模块的空间名称字符串作为第一个参数传递给上述函数，这样所有绑定都会自动将该模块作为上下文。于是上面的例子可以简化为：
```js
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```
而且，你可以通过使用 createNamespacedHelpers 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：
```js
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

##### 以上则为入门vuex需要学习的一些内容，更深入的了解则需老铁们在项目中的实践了，可到官网溜达溜达：https://vuex.vuejs.org/zh/