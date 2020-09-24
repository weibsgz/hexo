---
title: vue3 学习
date: 2020-09-23 14:13:23
tags: vue
category: "vue"
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

2. ref代码片段 注意ref 必有value

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
    const refData = toRefs(data)
    return {
      ...refData
    };
  }
};
</script>

```


### 生命周期对应
|  vue2   | vue3  |
|  :----:  |:----: |
| created | setup  |
| beforeCreate | setup |
| beforeMount	 |  onBeforeMount |
| mounted	    |   onMounted |
| beforeUpdate|	   onBeforeUpdate |
| updated	     |   onUpdated |
| beforeDestroy	|   onBeforeUnmount |
| destroyed	  |     onUnmounted |
| activated   |   onActivated |


onRenderTriggered  新增调试生命周期 

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

### hooks  模块化 

1. 场景：需要抽离一个鼠标位置的功能
2. 在src/hooks/useMousePosition.ts
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

### hooks  模块化 2

1. 场景: 接口请求 loading效果
2. 在src/hooks/useURLLoader.ts

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


###  一个弹窗组件  teleport（瞬间移动）
1.  在index.html 增加一个空DIV#modal 作为弹窗组件的父级，和#app并列（不会被业务样式干扰）
```
<body>
  <noscript>
    ...
  </noscript>
  <div id="app"></div>
  <div id="modal"></div>

</body>

```

2. modal组件 (teleport用法,emits需要作为一个option  否则报警告)
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
1. 不同于vue2  属性和方法都在vue上挂在，容易污染全局，vue3通过createApp创建app实例挂载

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

### VUE3 使用es6 import引入一些行为 可以更好的treeShaking
```
import {nextTick,observable} from 'vue'
```
1. 不同于vue2  使用 Vue.nextTick  Vue.observable这样可以treeShaking