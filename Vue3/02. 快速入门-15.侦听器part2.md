# 侦听器part2



## watchEffect

watchEffect 相比 watch 而言，能够自动跟踪回调里面的响应式依赖，对比如下：

watch

```js
const todoId = ref(1)
const data = ref(null)

watch(
  todoId, // 第一个参数需要显式的指定响应式依赖
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  },
  { immediate: true }
)
```

watchEffect

```js
// 不再需要显式的指定响应式数据依赖
// 在回调函数中用到了哪个响应式数据，该数据就会成为一个依赖
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

对于只有一个依赖项的场景来讲，watchEffect 的收益不大，但是如果涉及到多个依赖项，那么 watchEffect 的好处就体现出来了。

watchEffect 相比 watch 还有一个特点：如果你需要侦听一个嵌套的数据结构的几个属性，那么 watchEffect 只会侦听回调中用到的属性，而不是递归侦听所有的属性。

```vue
<template>
  <div>
    <h1>团队管理</h1>
    <ul>
      <li v-for="member in team.members" :key="member.id">
        {{ member.name }} - {{ member.role }} - {{ member.status }}
      </li>
    </ul>
    <button @click="updateLeaderStatus">切换领导的状态</button>
    <button @click="updateMemberStatus">切换成员的状态</button>
  </div>
</template>

<script setup>
import { reactive, watchEffect } from 'vue'
const team = reactive({
  members: [
    { id: 1, name: 'Alice', role: 'Leader', status: 'Active' },
    { id: 2, name: 'Bob', role: 'Member', status: 'Inactive' }
  ]
})

// 有两个方法，分别是对 Leader 和 Member 进行状态修改
function updateLeaderStatus() {
  const leader = team.members.find((me) => me.role === 'Leader')
  // 切换状态
  leader.status = leader.status === 'Active' ? 'Inactive' : 'Active'
}

function updateMemberStatus() {
  const member = team.members.find((member) => member.role === 'Member')
  member.status = member.status === 'Active' ? 'Inactive' : 'Active'
}

// 添加一个侦听器
watchEffect(() => {
  // 获取到 leader
  const leader = team.members.find((me) => me.role === 'Leader')
  // 输出 leader 当前的状态
  console.log('Leader状态:', leader.status)
})
</script>

<style scoped></style>
```



## 回调触发的时机

默认情况下，侦听器回调的执行时机在父组件更新 **之后**，所属组件的 DOM 更新 **之前** 被调用。这意味着如果你尝试在回调函数中访问所属组件的 DOM，<u>拿到的是 DOM 更新之前的状态</u>。

```vue
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <div v-if="isShow" ref="divRef">
      <p>this is a test</p>
    </div>
    <p>上面的高度为：{{ height }} pixels</p>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
const isShow = ref(false)
const height = ref(0) // 存储高度
const divRef = ref(null) // 获取元素

watch(isShow, () => {
  // 获取高度，将高度值给 height
  height.value = divRef.value ? divRef.value.offsetHeight : 0
  console.log(`当前获取的高度为：${height.value}`)
})
</script>

<style scoped></style>
```

如果我们期望侦听器的回调在 DOM 更新之后再被调用，那么可以将第三个参数 flush 设置为 post 即可，如下：

```js
watch(
  isShow,
  () => {
    // 获取高度，将高度值给 height
    height.value = divRef.value ? divRef.value.offsetHeight : 0
    console.log(`当前获取的高度为：${height.value}`)
  },
  {
    flush: 'post'
  }
)
```



## 停止侦听器

大多数情况下你是不需要关心如何停止侦听器，组件上面所设置的侦听器会在组件被卸载的时候自动停止。

但是上面所说的自动停止仅限于同步设置侦听器的情况，如果是异步设置的侦听器，那么组件被销毁也不会自动停止：

```vue
<script setup>
import { watchEffect } from 'vue'

// 它会自动停止
watchEffect(() => {})

// ...这个则不会！
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

这种情况下，就需要手动的去停止侦听器。

要手动的停止侦听器，就和 setTimeout 或者 setInterval 类似，调用一下返回的函数即可。

```js
const unwatch = watchEffect(() => {})
// 手动停止
unwatch();
```

下面是一个具体的示例：

```vue
<template>
  <div>
    <button @click="a++">+1</button>
    <p>当前 a 的值为：{{ a }}</p>
    <p>{{ message }}</p>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
const a = ref(1) // 计数器
const message = ref('') // 消息
// 假设我们期望 a 的值到达一定的值之后，停止侦听
const unwatch = watch(
  a,
  (newVal) => {
    // 当值大于 5 的时候，停止侦听
    if (newVal > 5) {
      unwatch()
    }
    message.value = `当前 a 的值为：${a.value}`
  },
  { immediate: true }
)
</script>

<style scoped></style>
```



---

-EOF-
