#  vuex

##  组件之间共享数据的方式

###  小范围的数据共享（不使用Vuex数据共享的方式）

* 父向子传值：props

* **v-bind属性**绑定

* 子向父传值:$emit(事件，参数)

* **v-on事件**绑定

* （**互不相干**）兄弟组件之间数据共享（main.js）
  * **EventBus**($Bus)
    * **$on 接收数据**的那个组件
    * **$emit发送数据**的那个组件

###  大范围的数据共享-----Vuex

* Vuex
  * 是实现组件**全局状态（数据）**管理的一种机制，可以方便的实现组件之间**数据的共享**

###  使用Vuex统一管理状态的好处？

* 能够**在vuex中集中管理共享的数据，易于开发和后期维护**
* 能够**高效的实现组件之间的数据共享**，提高开发效率
* 存储在vuex中的**数据都是响应的**，能够实时保持数据与页面同步

###  什么样的数据适合存储在Vuex中？

* 一般情况下，只有组件之间**共享的数据**，才有必要存储到vuex中，对于组件中的私有数据，依旧存储在组件自身的data中即可。

##  vuex的安装

* 下载

  * `npm install vuex  --save`

* 导入vuex包

  * ```javascript
    import Vuex from 'vuex'
    Vue.use(Vuex)
    ```

* 创建store对象

  * **state**中存放的就是**全局共享的数据**

  * ```javascript
    const store = new Vuex.store({
        //state中存放的就是全局共享的数据
        state: {
            count: 0
        }
    })
    ```

  * 

* 将store对象**挂载**到vue实例中

  * ```javascript
    new Vue = ({
        // 指定要控制的区域
        el: '#app',
        // 渲染根组件
        render: h => h(app),
        // 挂载路由
        router,
        // 将创建的共享数据对象，挂载到vue实例中
        // 所有的组件，就可以直接从store中获取全局的数据了
        store,
    })
    ```

* 在项目根目录下创建一个文件

  * `.prettierrc`的文件，并在里面配置如下信息,备注不能写入

  * ```javascript
    //格式化文档
    {
    	//	不使用分号
        "semi":false,
        //设为单引号
        "singleQuote":true,
        
    }
    ```

##  Vuex的核心概念

* 注意：下面的所有的事件绑定都可以直接绑定我们定义的函数，只要这个函数已经是自己的method了

###  State

* State提供**唯一的公共数据源**，所有**共享数据都要统一放到Store的State中进行存储**

* 在根目录下创建`store.ts`

  * ```typescript
    import Vue from 'vue'
    import Vuex from 'vuex'
    Vue.use(Vuex)
    export default new Vuex.Store({
        // state里面放全局共享的数据
        state: {
            count: 0,
        },
        mutations: {},
        actions: {},
    })
    ```

####  组件访问State中数据的方式

* 方式1：

  * `this.$store.state.全局数据名称`

  * ```vue
    <template>
      <div>
        <!-- 到用store.ts文件中的全局共享数据 ,在template中，this可以省略-->
        <!-- 注意，要在组件中使用$store,必须在main.ts中引入相关模块、并将共享对象挂载到vue中 -->
        <h3>当前的count的值为：{{ this.$store.state.count }}</h3>
        <button @click="可以自己定义函数，也可以直接使用methods里面的函数">+1</button>
      </div>
    </template>
    <script>
    export default {
      data() {
        return {}
      },
    }
    </script>
    ```

  * `main.ts`文件中

    ```typescript
    // 导入vue
    import Vue from 'vue'
    // 导入App跟组件
    import App from './App.vue'
    // 导入store
    import store from './store'
    Vue.config.productionTip = false
    new Vue({
      // 将创建的共享数据对象，挂载到vue实例中
      // 所有的组件，就可以直接从store中获取全局的数据了
      store,
      // 渲染根组件
      render: h => h(App)
    }).$mount('#app')
    ```

* 方法2：

  * 在相应的组件中导入：

    ```javascript
    //从vuex中按需导入mapState函数
    import {mapState} from 'vuex'
    ```

  * 通过导入的mapState函数，将当前组件需要的全局数据映射为当前组件的computed计算属性：

    `...`展开运算符

    * ```javascript
      //将全局数据映射为当前组件的计算属性
      computed:{
      	...mapState(['count'])
      }
      ```

    * ```vue
      <template>
        <div>
          <h3>当前的count的值为：{{count }}</h3>
          <button>+1</button>
        </div>
      </template>
      <script>vue
      //从vuex中按需导入mapState函数
      import { mapState } from "vuex"
      export default {
        data() {
          return {}
        },
        //将全局数据映射为当前组件的计算属性
        computed: {
          // 需要用到全局的什么属性，就将其放入数组中
          ...mapState(["count"]),
        },
      }
      </script>
      
      ```



### Mutation

* 作用：
  * 用于变更Store中的数据
    * **只能通过Mutation修改Store中的数据**，不能直接修改Store中的数据
    * 通过这种方式可以**集中监控所有数据的变化**

* 在`store.ts`文件中

  ```javascript
  import Vue from 'vue'
  import Vuex from 'vuex'
  Vue.use(Vuex)
  export default new Vuex.Store({
      // state里面放全局共享的数据
      state: {
          count: 0,
      },
      mutations: {
          //放置更改Store中数据的方法
          add(state) {
              //变更状态
              state.count++;
          },
          sub(state) {
              state.count--;
          }
      },
      actions: {},
  })
  ```

* 

###  如何在组件中调用Mutations里面的对应函数呢？

* **方式1：**

  * `this.$store.commit("store.js文件定义的函数")`

  * handler是绑定事件的函数

  ```javascript
    // 触发mutation
    methods: {
      handler() {
        //触发mutations的第一种方式
  	 //commit的作用就是调用某个mutation函数
        this.$store.commit("add")
      },
    },
  }
  ```

  * 在对应的组件中

```vue
<template>
  <div>
    <!-- 到用store.ts文件中的全局共享数据 ,在template中，this可以省略-->
    <!-- 注意，要在组件中使用$store,必须在main.ts中引入相关模块 -->
    <h3>当前的count的值为：{{ this.$store.state.count }}</h3>
    <h3>当前的count的值为：{{ count }}</h3>
    <button @click="handler">+1</button>
  </div>
</template>
<script>
//从vuex中按需导入mapState函数
import { mapState } from "vuex"
export default {
  data() {
    return {}
  },
  //将全局数据映射为当前组件的计算属性
  computed: {
    // 需要用到全局的什么属性，就将其放入数组中
    ...mapState(["count"]),
  },
  // 触发mutation
  methods: {
    handler() {
      //触发mutations的第一种方式
     //commit的作用就是调用某个mutation函数
      this.$store.commit("add")
    },
  },
}
```

* **方式2：**

  * 在对应的组件中按需导入mapMustations,将需要的mutation函数映射为当前组件的methods方法：

    * `import { mapMutations } from "vuex"`

    * ```javascript
        // 触发mutation
        methods: {
          //触发mutations的第2种方式,数组里面的数据就是store.js的mutations中的函数
          ...mapMutations(["add", "addn"]),
          handler() {
        	//表示传递的第二个参数是3
            this.addn(3)
          },
        },
      ```

      

###  如何在调用Mutations的时候传递参数？

* 在Mutations中，**定义函数**的时候，给他传参数

  ```javascript
  mutations: {
          //放置更改Store中数据的方法
          add(state,n) {
              //变更状态
              state.count+=n;
          },
          sub(state) {
              state.count--;
          }
      },
  ```

* 在调用的时候传递**实参**

  ```javascript
    // 触发mutation
    methods: {
      handler() {
        //触发mutations的第一种方式
  	  //触发mutations时携带参数
  	 //commit的作用就是调用某个mutation函数
        this.$store.commit("add",3)
      },
    },
  ```

* **注意，不允许在 mutations中执行异步操作**

###  Action

* 用于处理异步任务

  * Action 通过触发Mutation的中的数据，从而变更数据

  * 在store.js文件中

    ```javascript
        actions: {
            //context相当于new出来的vuex.store
            addAsync(context, n) {
                setTimeout(() => {
                    // 在actions中不能直接修改state中的数据，如果需要修改state中的数据，必须通过context.commit触发某个mutation才行
                    context.commit('add', n)
                }, 1000);
            }
        },
    
    ```

  * 在对应的组件中

    ```javascript
      // 触发mutation
      methods: {
        handler() {
          // 调用Action 中的方法
          // dispatch的作用就是用来触发action中函数
          this.$store.dispatch("addAsync", 3)
        },
      },
    ```

###   触发actions的方式

* **方式1**：如上面的代码
  
* `this.$store.dispatch("actions中定义的函数名称"，实际参数)`
  
* **方式2：**

  * 导入mapActions
    * `import { mapActions } from "vuex"`
  * 将需要的mapActions函数映射为当前组件的methods方法：

  ```javascript
    methods: {
      // 调用Action方法2
      ...mapActions(["addAsync"]),
      handler() {
        // 这里的实参就是函数的第二个参数
        this.addAsync(3)
      },
    },
  ```

**注意：绑定的函数形式，这两种写法都对**

* 方式1：

  ```vue
      <h3>当前的count的值为：{{ count }}</h3>
      <button @click="handler">+n</button>
      <script>
  import { mapState, mapMutations, mapActions } from "vuex"
  export default {
    data() {
      return {}
    },
    //将全局数据映射为当前组件的计算属性
    computed: {
      // 需要用到全局的什么属性，就将其放入数组中
      ...mapState(["count"]),
    },
    // 触发mutation
    methods: {
      ...mapActions(["addAsync"]),
      handler() {
        // 这里的实参就是函数的第二个参数
        this.addAsync(3)
      },
    },
  }
  </script>
  
  ```

* 方式2：

  ```vue
      <h3>当前的count的值为：{{ count }}</h3>
      <button @click="addAsync(3)">+n</button>
  <script>
  import { mapState, mapMutations, mapActions } from "vuex"
  export default {
    data() {
      return {}
    },
    //将全局数据映射为当前组件的计算属性
    computed: {
      // 需要用到全局的什么属性，就将其放入数组中
      ...mapState(["count"]),
    },
    // 触发mutation
    methods: {
      ...mapActions(["addAsync"]),
    },
  }
  </script>    
  ```

  

###    Getter

* Getter用于对store中的数据进行加工处理，形成新的数据（不会更改store中的数据，只是起到包装的作用）

  * 对store中的数据进行加工处理，形成新的数据，类似域vue中的计算属性
  * Store中数据发生变化，Getter中的数据也会跟着变化

  ```javascript
      // 用于包装数据（加工处理数据，不会更改）
      getters: {
          // 定义一个函数,他的第一个参数必须是state对象
          showNum: state => {
              // 返回的值就是包装后的数据
              return `当前count是${state.count}`;
          }
  
      }
  ```

###  使用getter的方式

* 方法1：

  * 在对应的组件中的**模板**中
    * `this.$store.getters.定义的函数名称`

* 方法2：

  * 导入相关的包

    * `import  { mapGetters } from "vuex"`

  * 通过导入的mapGetters函数，将当前组件需要的全局数据映射为当前组件的computed计算属性：

    * ```javascript
        computed: {
          ...mapGetters(["showNum"]),
        },
      ```

  * 直接通过插值表达式调用
    
    * `  <h3>{{ showNum }}</h3>`

## 所有的方法1

* store.js

```typescript
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
export default new Vuex.Store({
    // state里面放全局共享的数据
    state: {
        count: 0,
    },
    // 只有mutations中定义的函数才有权利修改state中的数据
    mutations: {
        // 注意，不允许在中执行异步操作
        //放置更改Store中数据的方法
        add(state, n) {
            //变更状态
            state.count += n;
        },
        sub(state) {
            state.count--;
        }
    },
    actions: {
        //context相当于new出来的vuex.store
        addAsync(context, n) {
            setTimeout(() => {
                // 在actions中不能直接修改state中的数据，如果需要修改state中的数据，必须通过context.commit触发某个mutation才行
                context.commit('add', n)
            }, 1000);
        }
    },
    // 用于包装数据（加工处理数据，不会更改）
    getters: {
        // 定义一个函数,他的第一个参数必须是state对象
        showNum: state => {
            // 返回的值就是包装后的数据
            return `当前count是${state.count}`;
        }

    }
})
```

* 对应子组件

  ```vue
  <template>
    <div>
     	//调用state
    	<h3>当前的count的值为：{{ this.$store.state.count }}</h3>
    	//调用getter
    	<h3>{{ this.$store.getters.showNum }}</h3>
     	<button @click="handler">+n</button>
    </div>
  </template>
  <script>
  export default {
    data() {
      return {}
    },
    // 触发mutation
    methods: {
      handler() {
      //触发mutations
      // commit的作用就是调用某个mutation函数
      this.$store.commit("add", 3)
      // 调用Action 中的方法
      // dispatch的作用就是用来触发actions中函数
      this.$store.dispatch("addAsync", 3)
      },
    },
  }
  </script>
  
  ```

  



##  所有的方法2

* js代码一样

```vue
<template>
  <div>
      //引用state里面的共享数据count
	<h3>当前的count的值为：{{ count }}</h3>
      //使用getters中的函数showNum
    <h3>{{ showNum }}</h3>
    <button @click="handler">+n</button>
  </div>
</template>
<script>
//方法2：必须导入这个
import { mapState, mapMutations, mapActions, mapGetters } from "vuex"
export default {
  data() {
    return {}
  },
  //将全局数据映射为当前组件的计算属性
  computed: {
    // 需要用到全局的什么属性，就将其放入数组中
    //调用state中的共享数据，然后用插值表达式引入页面就行
    ...mapState(["count"]),
    //调用getters中的函数，然后用插值表达式引入页面就行
    ...mapGetters(["showNum"]),
  },
  // 触发mutation
  methods: {
    //触发mutations
     ...mapMutations(["add"]),
     handler() {
       this.add(3)
     },

    // 调用Action方法2
    ...mapActions(["addAsync"]),
    handler() {
      // 这里的实参就是函数的第二个参数
      this.addAsync(3)
    },
  },
}
</script>

```

