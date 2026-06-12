# 侦听器part1

侦听器和计算属性类似，都是依赖响应式数据。不过计算属性是在依赖的数据发生变化的时候，重新做二次计算，不会涉及到副作用的操作。而侦听器则刚好相反，在依赖的数据发生变化的时候，允许做一些副作用的操作，例如更改 DOM、发送异步请求...



## 快速入门

```vue
<template>
  <div>
    <h1>智能机器人</h1>
    <div>
      <input v-model="question" placeholder="请输入问题" />
    </div>
    <div v-if="loading">正在加载中...</div>
    <div v-else>{{ answer }}</div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
const question = ref('') // 存储用户输入的问题，以 ？ 结束
const answer = ref('') // 存储机器人的回答
const loading = ref(false) // 是否正在加载中
// 侦听器所对应的回调函数，接收两个参数
// 一个是依赖数据的新值，一个是依赖数据的旧值
watch(question, async (newValue) => {
  if (newValue.includes('?')) {
    loading.value = true
    answer.value = '思考中....'
    try {
      const res = await fetch('https://yesno.wtf/api')
      const result = await res.json()
      answer.value = result.answer
    } catch (err) {
      answer.value = '抱歉，我无法回答您的问题'
    } finally {
      loading.value = false
    }
  }
})
</script>

<style scoped></style>
```

在上面的示例中，watch 就是一个侦听器，侦听 question 这个 ref 状态的变化，每次当 ref 状态发生变化的时候，就会重新执行后面的回调函数，回调函数接收两个参数：

- 新的状态值
- 旧的状态值

并且在回调函数中，支持副作用操作。



## 各种细节



### 1. 侦听的数据源类型

除了上面快速入门中演示的侦听 ref 类型的数据以外，还支持侦听一些其他类型的数据。

**计算属性**

```vue
<template>
  <div>
    <input type="text" v-model="firstName" placeholder="first name" />
    <input type="text" v-model="lastName" placeholder="last name" />
    <p>全名：{{ fullName }}</p>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
const firstName = ref('John')
const lastName = ref('Doe')
const fullName = computed(() => `${firstName.value} ${lastName.value}`)

// 设置侦听器
watch(fullName, (newVal, oldVal) => {
  console.log(`new: ${newVal}, old: ${oldVal}`)
})
</script>

<style scoped></style>
```

**reactive响应式对象**

```vue
<template>
  <div>
    <input type="text" v-model="user.name" placeholder="name" />
    <input type="text" v-model="user.age" placeholder="age" />
    <p>用户信息：{{ user.name }} - {{ user.age }}</p>
  </div>
</template>

<script setup>
import { reactive, watch } from 'vue'
const user = reactive({
  name: 'John',
  age: 18
})

// 设置侦听器
watch(user, () => {
  console.log('触发了侦听器回调函数')
})
</script>

<style scoped></style>
```

**Getter函数**

```vue
<template>
  <div>
    <input type="number" v-model="count" />
    <p>是否为偶数？{{ isEven() }}</p>
    <div>count2: {{ count2 }}</div>
    <button @click="count2++">+1</button>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
const count = ref(0)
const count2 = ref(0)

// 注意这个函数本身，是每次重新渲染的时候都会重新执行的
function isEven() {
  console.log('isEvent 函数被重新执行了')
  if (count2.value === 5) {
    return 'this is a test'
  }
  return count.value % 2 === 0
}
// 设置侦听器
// 这里侦听的是函数的返回值结果
// 如果函数返回值发生变化，就会触发侦听器回调函数
watch(isEven, () => {
  console.log('触发了侦听器回调函数')
})
</script>

<style scoped></style>
```

**多个数据源所组成的数组**

```vue
<template>
  <div>
    <div>
      <input type="text" v-model="title" />
    </div>
    <div>
      <textarea v-model="description" cols="30" rows="10"></textarea>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
const title = ref('')
const description = ref('')
// 这里侦听的是多个数据源所组成的数组
// 数组里面任何一个数据发生变化，都会触发回调函数
watch([title, description], () => {
  console.log('侦听器的回调函数执行了')
})
</script>

<style scoped></style>
```



### 2. 侦听层次

这个主要是针对 reactive 响应式对象，当侦听的数据源是 reactvie 类型数据的时候，默认是深层次侦听，这意味着哪怕是嵌套的属性值发生变化，侦听器的回调函数也会重新执行。

```vue
<template>
  <div>
    <h1>任务列表</h1>
    <ul>
      <li v-for="task in tasks.list" :key="task.id">
        {{ task.title }} - {{ task.completed ? '已完成' : '未完成' }}
        <button @click="task.completed = !task.completed">切换状态</button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { reactive, watch } from 'vue'
const tasks = reactive({
  list: [
    { id: 1, title: 'Task 1', completed: false },
    { id: 2, title: 'Task 2', completed: true }
  ]
})

watch(tasks, () => {
  console.log('侦听器触发了！')
})
</script>

<style scoped></style>
```

通过上面的例子，我们可以看出，当侦听的是 reactive 类型的响应式对象时，是深层次侦听的。

虽然上面的这个深层次侦听的特性非常的方便，但是当用于大型数据结构的时候，开销也是很大的，因此一定要留意性能，只在必要的时候再使用。



另外补充一个点，当侦听的是 reactive 对象的时候，不能直接侦听响应式对象的属性值：

```js
const obj = reactive({ count: 0 })

// 错误，因为 watch() 得到的参数是一个 number
watch(obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```

可以将上面的例子修改为一个 Getter 函数：

```js
const obj = reactive({ count: 0 })

watch(()=>obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```



### 3. 第三个参数

- 第一个参数：侦听的数据源
- 第二个参数：数据发生变化时要执行的回调函数
- 第三个参数：选项对象
  - immediate：true/false
    - 默认情况下，watch 对应的回调函数是懒执行的，只有在依赖的数据发生变化时，才会执行回调。
    - 但是在某些场景中，我们可能期望立即执行一次，例如请求一些初始化数据，这个时候就可以设置该配置项
  - once：true/false
    - 侦听器的回调函数只执行一次
  - deep：true/false
    - 强制转换为深层次侦听器
    - 什么时候会用到呢？有些时候使用 watch 来侦听一个由计算属性或者 getter 函数返回的对象的时候，默认就不是深层次的侦听
    - 通过设置 deep 可以让这种情况下的对象侦听，也变成深层次的侦听

```vue
<template>
  <div>
    <div v-for="task in tasks" :key="task.id" @click="selectTask(task)">
      {{ task.title }} ({{ task.completed ? 'Completed' : 'Pending' }})
    </div>
    <hr />
    <div v-if="selectedTask">
      <h3>Edit Task</h3>
      <input v-model="selectedTask.title" placeholder="Edit title" />
      <label>
        <input type="checkbox" v-model="selectedTask.completed" />
        Completed
      </label>
    </div>
  </div>
</template>

<script setup>
import { reactive, computed, watch } from 'vue'

const tasks = reactive([
  { id: 1, title: 'Learn Vue', completed: false },
  { id: 2, title: 'Read Documentation', completed: false },
  { id: 3, title: 'Build Something Awesome', completed: false }
])

const selectedId = reactive({ id: null })

// 这是一个计算属性
const selectedTask = computed(() => {
  return tasks.find((task) => task.id === selectedId.id)
})

// 侦听的是一个 Getter 函数
// 该 Getter 函数返回计算属性的值
watch(
  () => selectedTask.value,
  () => {
    console.log('Task details changed')
  },
  { deep: true }
)

function selectTask(task) {
  selectedId.id = task.id
}
</script>
```



---

-EOF-
