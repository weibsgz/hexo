---
title: vue3 学习
date: 2020-09-23 14:13:23
tags: vue
category: 'vue'
---

### ref 和 reactive

1. 我的理解是 原则上是区分基本类型(ref)和引用类型(reactive)的。功能是一样的，其实是两种风格的代码

```
// 风格1
let age = 10
let name = 'zhangsan'

function updatePerson() {
  age = 20
}

// 风格2
let person = {
  age: 10,
  name: 'zhangsan',
  sex: 'male'
}

function updatePerson() {
  person.age = 20
}

```

2. ref 代码片段 注意 ref 必有 value

```
<template>
  <div id="app">
    <h1>{{count}}</h1>
    <h1>{{double}}</h1>
    <button @click="increase">+1</button>
  </div>

</template>

<script>
import { ref, computed, reactive } from 'vue'
export default {
  name: "App",
  setup() {
    const count = ref(0)
    const double = computed(() => {
      return count.value * 2
    })

    const increase = () => {
      count.value++;
    }
    return {
      count, increase, double
    }
  }
};
</script>

```

3. reactive 注意返回对象 形式（toRefs）

```
<template>
  <div id="app">
    <h1>{{count}}</h1>
    <h1>{{double}}</h1>
    <button @click="increase">+1</button>
  </div>

</template>

<script>
import { computed, reactive, toRefs } from "vue";
export default {
  name: "App",
  setup() {
    const data = reactive({
      count: 0,
      increase: () => {
        //这里不需要value了
        data.count++;
      },
      double: computed(() => {
        return data.count * 2;
      })
    });

    //直接返回data 也没发现什么问题
    // return data

    //如果要返回对象，用展开运算符 需要用 toRefs包装一下，让展开后都变成响应式对象
    //这样 template中 直接用 count 可以不用 data.count
    const refData = toRefs(data)
    return {
      ...refData
    };
  }
};
</script>

```

### 生命周期对应

|     vue2      |      vue3       |
| :-----------: | :-------------: |
|    created    |      setup      |
| beforeCreate  |      setup      |
|  beforeMount  |  onBeforeMount  |
|    mounted    |    onMounted    |
| beforeUpdate  | onBeforeUpdate  |
|    updated    |    onUpdated    |
| beforeDestroy | onBeforeUnmount |
|   destroyed   |   onUnmounted   |
|   activated   |   onActivated   |

onRenderTriggered 新增调试生命周期

```
import { onMounted, onUpdated, onRenderTriggered } from "vue";
export default {
  name: "App",
  setup() {
    onMounted(() => {
      console.log('onMounted')
    })
    onUpdated(() => {
      console.log('onUpdated')
    })
    //调试用，数据变更可以看到属性
    onRenderTriggered((event) => {
      console.log(event)
    })


    打印的event 如下：
    effect: ƒ reactiveEffect()
    key: "count"
    newValue: 1
    oldTarget: undefined
    oldValue: 0
    target: {count: 1, double: ComputedRefImpl, increase: ƒ}
    type: "set"


```

### watch

```
import { computed, reactive, toRefs, ref, watch } from "vue";
export default {
  name: "App",
  setup() {

    const data = reactive({
      count: 0,
      increase: () => {
        //这里不需要value了
        data.count++;
      },
      double: computed(() => {
        return data.count * 2;
      })
    });

    const greetings = ref('')
    const updateGreeting = () => {
      greetings.value += 'Hello!'
    }

    //只观察一个
    // watch(greetings, (newV, oldV) => {
    //   document.title = 'update' + greetings.value
    // })

    //观察多个对象 打印的 newV 和 oldV 分别是数组  一个代表第greetings，第二个是count
    //注意 如果直接观察data 打印出个proxy 想精确观察data.count 需要函数返回，否则
    //watch中不接受data.count 因为他表示一个数字 没法watch
    watch([greetings, () => data.count], (newV, oldV) => {
      console.log(newV, oldV)  //点击updateGreeting第一次打印  ["Hello!", 0] ， ["", 0]
      document.title = 'update' + greetings.value + data.count;
    })

    //直接返回data 也没发现什么问题
    // return data

    //如果要返回对象，用展开运算符 需要用 toRefs包装一下，让展开后都变成响应式对象
    const refData = toRefs(data)
    return {
      ...refData,
      updateGreeting,
      greetings
    };
  }
};

```

### computed

1. 如果一个对象中包含响应式的属性，需要用 computed 包裹这个对象返回
2. 注意 computed 包裹对象后 需要.value

```
const currentPage = ref(params.currentPage);
 const requestParams = computed(() => ({
    currentPage: currentPage.value,
    pageSize: params.pageSize
  }))

  //点击加载更多
  const loadMorePage = () => {
    store.dispatch(actionName, requestParams.value).then(() => {
      currentPage.value++;
    })
  }

```

### hooks 模块化

1. 场景：需要抽离一个鼠标位置的功能
2. 在 src/hooks/useMousePosition.ts

```
import { ref, onMounted, onUnmounted } from 'vue'

export default function useMousePosition() {
  const x = ref(0);
  const y = ref(0);

  const updateMouse = (e: MouseEvent) => {
    x.value = e.pageX;
    y.value = e.pageY;
  }

  onMounted(() => {
    document.addEventListener('click', updateMouse)
  })

  onUnmounted(() => {
    document.removeEventListener('click', updateMouse)
  })

  return {
    x, y
  }
}
```

3. 在任意组件中使用

```
<template>
  <div>
    <h1>X:{{x}}, Y:{{y}}</h1>
  </div>
</template>

import useMousePosition from './hooks/useMousePosition.ts'
export default {
  name: "App",
  setup() {
    const { x, y } = useMousePosition()
    return {
      x, y
    };

  }

```

### hooks 模块化 2

1. 场景: 接口请求 loading 效果
2. 在 src/hooks/useURLLoader.ts

```
import axios from 'axios'
import { ref } from 'vue'

export default function useURLLoader(url: string) {
  const result = ref(null)
  const loading = ref(true)
  const loaded = ref(false)
  const error = ref(null)

  axios.get(url).then(res => {
    loading.value = false;
    loaded.value = true;
    result.value = res.data;
  }).catch(err => {
    error.value = err;
    loading.value = false;
  })

  return {
    result, loading, error, loaded
  }
}
```

3. 在任意组件中使用

```
<template>
  <div id="app">
    <div v-if="loading">loading</div>
    <div v-if="loaded"><img :src="result.message" /></div>
  </div>
</template>

<script>
import { computed, reactive, toRefs, ref, watch } from "vue";
import useMousePosition from './hooks/useMousePosition.ts'
import useURLLoader from './hooks/useURLLoader.ts'


export default {
  name: "App",
  setup() {
    const { result, loading, loaded, error } = useURLLoader('https://dog.ceo/api/breeds/image/random')

  return {
      result,
      loading,
      error,
      loaded
  };
}

```

### 一个弹窗组件 teleport（瞬间移动）

1.  在 index.html 增加一个空 DIV#modal 作为弹窗组件的父级，和#app 并列（不会被业务样式干扰）

```
<body>
  <noscript>
    ...
  </noscript>
  <div id="app"></div>
  <div id="modal"></div>

</body>

```

2. modal 组件 (teleport 用法,emits 需要作为一个 option 否则报警告)

```
<template>
  <teleport to="#modal">
    <div id="center" v-if="isOpen">
      <h2>
        <slot></slot>
      </h2>
      <button @click="closeHandler">关闭</button>
    </div>
  </teleport>
</template>
<script lang="ts">
export default {
  props: {
    isOpen: {
      type: Boolean,
      default: false
    }
  },
  emits: ["close-modal"],
  setup(props, context) {
    const closeHandler = () => {
      context.emit("close-modal");
    };

    return {
      closeHandler
    };
  }
};
</script>
<style>
#center {
  width: 200px;
  height: 200px;
  border: 2px solid black;
  background: white;
  position: fixed;
  left: 50%;
  top: 50%;
  margin-left: -100px;
  margin-top: -100px;
}
</style>
```

3. 正常在业务组件中使用

```
<template>
  <div id="app">
    <button @click="onModalOpen">打开窗口</button>
    Modal :isOpen="modalIsOpen" @close-modal="onModalClose">my modal...</Modal>
  </div>
</template>


setup() {
  const modalIsOpen = ref(false)
  const onModalOpen = () => {
    modalIsOpen.value = true;
  }
  const onModalClose = () => {
    modalIsOpen.value = false;
  }
}
```

### 异步组件 supense

1. 场景：组件内容需要异步加载

AsyncShow.vue

```
<template>
  <h1>{{result}}</h1>
</template>
<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  setup() {
    return new Promise(resovle => {
      setTimeout(() => {
        return resovle({
          result: "异步组件supense!"
        });
      }, 2000);
    });
  }
});
</script>
```

2. 使用

```
  <template #default>
    <AsyncShow></AsyncShow>
    //这里也可以包括多个组件
  </template>
  <template #fallback>
    <h1>loading</h1>
  </template>

```

### createApp

1. 不同于 vue2 属性和方法都在 vue 上挂在，容易污染全局，vue3 通过 createApp 创建 app 实例挂载

```
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App)

app.use(...)
app.mixin(...)
app.directive(...)
app.component(...)


app.mount('#app')

```

### VUE3 使用 es6 import 引入一些行为 可以更好的 treeShaking

```
import {nextTick,observable} from 'vue'
```

1. 不同于 vue2 使用 Vue.nextTick Vue.observable 这样可以 treeShaking

### hooks & 点击其他地方 让下拉菜单关闭

1. hooks/useClickOutside.js

```
//点击任意位置 看是否在指定元素内 （用做点任意位置收起下拉菜单）
import { ref, onMounted, onUnmounted } from "vue";
const useClickOutside = (elementRef) => {
  const isClickOutside = ref(false);
  const handleClick = (e) => {
    if (elementRef.value) {
      isClickOutside.value = (elementRef.value.contains(e.target)) ? false : true
    }
  }

  onMounted(() => {
    document.addEventListener("click", handleClick);
  });

  onUnmounted(() => {
    document.removeEventListener("click", handleClick);
  });

  return isClickOutside
}

export default useClickOutside

```

2. 使用

```
<template>
  <div class="dropdown" ref="dropDownRef">
    <a href="#" class="btn btn-outline-light my-2 dropdown-toggle" @click.prevent="toggleOpen">
      {{title}}
    </a>
    <ul class="dropdown-menu" :style="{display: 'block'}" v-if="isOpen">
      <slot></slot>
    </ul>
  </div>
</template>


<script lang="ts">
import { defineComponent, ref, watch } from "vue";
import useClickOutside from "../hooks/useClickOutside.js";
export default defineComponent({
  name: "Dropdown",
  props: {
    title: {
      type: String,
      required: true
    }
  },

  setup() {
    const isOpen = ref(false);
    //ref
    const dropDownRef = ref(null);

    const isClickOut = useClickOutside(dropDownRef);

    watch(isClickOut, () => {
      if (isClickOut.value && isOpen.value) {
        isOpen.value = false;
      }
    });

    const toggleOpen = e => {
      isOpen.value = !isOpen.value;
    };

    return {
      isOpen,
      toggleOpen,
      dropDownRef  //ref
    };
  }
});
</script>
```

### 组件 v-model

v-model 语法语法糖拆解

1. 组件内部 input 去掉 v-model 改成 :value = "inputRef.val"
2. 组件内部 input 增加 @input 方法 其实就是不用 v-model 语法糖了
3. 组件内部 props 上增加 modelValue
4. 定义@input 方法 并在方法内部 emit('update:modelValue')
5. 父组件中使用 `<child v-model="emailVal"></child>`

子组件

```
<input :value="inputRef.val" @input="updateValue">


export default {
  props: {
    modelValue: String   //组件绑定v-model 必须这么写
  },
  setup(props, { emit }) {
    const inputRef = reactive({
      val: props.modelValue || '',
    });

    const updateValue = (e) => {
      inputRef.val = e.target.value;
      //组件中绑定v-model的固定写法emit('update:modelValue'
      emit('update:modelValue', e.target.value)
    }

```

父组件 直接使用

```
<ValidateInput placeholder="shit" v-model="emailVal" :rules="emailRules"></ValidateInput>
{{emailVal}}
const emailVal = ref("weibin"); //默认值
return {
  emailVal
}

```

### 组件 v-model 实践

父组件 可以传递多个 v-model

```
<div id="app">
  <h3>vue3自定义组件的v-model</h3>
  <p>msg: {{msg}} msg1: {{msg1}}</p>
  <div class="child">
    <h4>自定义input组件</h4>
    <custom-input v-model:mv="msg"  v-model:mv1="msg1"></custom-input>
    <!-- <custom-input :model-value="msg" @update:model-value="msg = $event"></custom-input> -->
  </div>
  <p>count: {{count}}</p>
  <div class="child">
    <h4>自定义count组件</h4>
    <custom-count v-model="count"></custom-count>
    <!-- <custom-count :model-value="count" @update:model-value="count = $event"></custom-count> -->
  </div>
</div>


<script>

const App = {
  data(){
    return {
      msg: 'Hey! young guy.',
      msg1:'test',
      count: 0
    }
  }
};

const app = Vue.createApp(App);

//两种写法 直接子组件用v-model  或者  用拆开的写法 :value="mv1" @input="onNameInput" 可以直接发送给父组件 父组件直接接就完事了 双向绑定
app.component('custom-input', {
  props: ['mv','mv1'],
  computed:{
    query: {
      get(){
        return this.mv
      },
      set(v){
        this.$emit('update:mv', v);
      }
    }
  },
  methods:{
    onNameInput(e){
      console.log(e.target.value)
      this.$emit('update:mv1',e.target.value)
    }
  },
  template: `
    <input v-model="query">
    <input type="text" :value="mv1" @input="onNameInput"/>
  `,
});

app.component('custom-count', {
  props: {
    modelValue: Number,
  },
  methods: {
    increment() {
      this.$emit('update:modelValue', ++this.modelValue);
    },
    decrement() {
      this.$emit('update:modelValue', --this.modelValue);
    },
  },
  template: `
    <button @click="increment">+1</button> ~
    <button @click="decrement">-1</button>
    <p>{{modelValue}}</p>
  `,
});

app.mount('#app');

```

### 路由

1. 路由配置 main.ts

```
import { createRouter, createWebHistory } from 'vue-router'

import Home from './views/Home.vue'
import Login from './views/Login.vue'
import ColumnDetail from './views/ColumnDetail.vue'
//替代 2.0 中 的 mode
const routerHistory = createWebHistory()
const router = createRouter({
  history: routerHistory,
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    },
    {
      path: '/Login',
      name: 'Login',
      component: Login
    },
    {
      path: '/column/:id',
      name: "column",
      component: ColumnDetail
    }
  ]
})
const app = createApp(App)
app.use(router)
app.mount('#app')
```

1. 使用 useRoute 获取路由信息

```
import { useRoute } from "vue-router";
export default defineComponent({
  setup() {
    const route = useRoute();
    return {
      route
    };
  }
});

//这样页面上就可以通过 route.params.xxx 来获取信息了
```

2. 使用 userRouter 来设置路由的行为

```
import { useRouter } from "vue-router";
setup() {
    const router = useRouter();
    //配置路由设置了 path: '/column/:id', 这里必须有params
    router.push({ name: "column", params: { id: 1 } });

  }
```

### vuex

1. main.js

```
import store from './store'

const app = createApp(App)
app.use(router)
app.use(store)
app.mount('#app')
```

2. store.js

```
import { createStore } from 'vuex'
import { testData, testPosts } from './testData'

const store = createStore({
  state: {
    columns: testData,
    posts: testPosts,
    user: { isLogin: false, name: "" }
  },
  mutations: {
    login(state) {
      state.user = { ...state.user, isLogin: true, name: 'weibin' }
      console.log(state.user)
    }
  },
  getters: {
    biggerColumnsLen(state) {
      return state.columns.filter(c => c.id > 2).length
    }
  }
})

export default store

```

3. 组件内使用

```
import { useStore } from "vuex";

setup() {
    const store = useStore();
    const list = computed(() => store.state.columns);
    console.log(list);
    return {
      list: list
    };
  }`
```

commit

```
setup() {
  const store = useStore();
  store.commit("login");
}

```

4. getters
   getters 的作用是可以将 state 的值做一些处理
   否则在多地方用 都要自己写一遍 computed 处理

```
getters: {
    biggerColumnsLen(state) {
      return state.columns.filter(c => c.id > 2).length
    }
  }

```

```
   const bigger = computed(() => store.getters.biggerColumnsLen);

   return {
      bigger
    };

```

5. getters 也可以是一个函数 传入参数

```
getters: {
    getColumnById(state) {
      return (id: number) => {
        return state.columns.find(c => c.id === id)
      }
    },
    getPostsByCid(state) {
      return (cid: number) => {
        return state.posts.filter(post => post.columnId === cid)
      }
    }
  }

```

使用

```
setup() {

    const store = useStore();
    const currentId = +route.params.id;

    const column = computed(() =>
      store.getters.getColumnById(currentId)
    );

    const list = computed(() =>
      store.getters.getPostsByCid(currentId)
    );

    return {
      column,
      list
    };
  }

```

### classList 设置在 DOM 上添加和删除一个类

1. 比如 $el对象  （一般是VUE实例关联的DOM对象 本项目中components/base/loading/directive.js中用到）
   或者 dom 上的 ref    (this.$refs.xxx)

   ```
      this.$refs.recommend.classList.add('test'),
      this.$el.classList.remove('recommend')
   ```

   以上的\$el 指当前 vue 组件上的根节点

   本项目中根据指令封装

   ```
   export function addClass(el, className) {
   if (!el.classList.contains(className)) {
     el.classList.add(className)
   }
   }

   export function removeClass(el, className) {
   el.classList.remove(className)
   }

   ```

### loading 自定义指令 （含动态参数）

1. 使用

```
<div class="recommend" v-loading:[loadingText]="loading"></div>

  data() {
    return {
      loadingText: '疯狂加载中。。。',
    }
  },
   computed: {
    loading() {
      //数据是否加载
      return !this.sliders.length && !this.albums.length
    },
  },
```

2. main.js 导入

```
import loadingDirective from '@/components/base/loading/directive'
createApp(App)
  .use(store)
  .use(router)
  .use(lazyPlugin, {
    loading: require('@/assets/images/default.png'),
  })
  .directive('loading', loadingDirective)
  .mount('#app')


```

3. 具体代码见 vue3-music base/loading

### 过渡动画

1. 点击按钮进入子路由过渡,屏幕右侧抽屉效果

css

```
.slide-enter-active,.slide-leave-active {
  transition:al 0.3s
}

.slide-enter-from, .slide-leave-to {
  transform:translate3d(100%,0,0)
}

```

2. 路由部分

name="slide" slide 对应样式 slide-enter-active....
appear 代表进入就有动画

```
<router-view v-slot="{Component}">
  <transition appear name="slide">
    <component :is="Component">
  </transition>
 </router-view>

```
