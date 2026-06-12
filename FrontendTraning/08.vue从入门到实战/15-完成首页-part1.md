# 15. 完成首页-part1

## 课程概述

本章节将完成首页的轮播图功能，包括整体布局、轮播图切换、鼠标滚轮事件监听等功能。我们将采用组件化开发思想，将首页拆分为 `Home` 组件和 `CarouselItem` 组件。

## 学习目标

- 理解组件职责划分原则
- 掌握轮播图实现的原理和技巧
- 学会使用动态样式控制页面效果
- 了解如何通过API获取数据并在组件中展示

## 组件架构设计

### 职责划分

我们采用**单一职责原则**来设计组件：

- **Home组件**：负责整体布局和交互控制
  - 整体页面布局
  - 监听鼠标滚轮事件，切换轮播图
  - 提供上下导航按钮
  - 提供右侧指示器
  
- **CarouselItem组件**：负责单张轮播图的内容展示
  - 单张轮播图的全部事务
  - 背景图片显示
  - 文字内容展示

```
┌─────────────────────────────────┐
│           Home Component         │
│  ┌───────────────────────────┐  │
│  │   CarouselItem Component  │  │
│  │   (单张轮播图内容)         │  │
│  └───────────────────────────┘  │
│  ┌───────────────────────────┐  │
│  │   CarouselItem Component  │  │
│  │   (单张轮播图内容)         │  │
│  └───────────────────────────┘  │
│  [↑]                    [● ○ ○] │
│  [↓]                          │  │
└─────────────────────────────────┘
```

## API层实现

### banner.js - 轮播图API

```javascript
// src/api/banner.js
import request from "./request";

/**
 * 获取轮播图数据
 * @returns {Promise} 返回轮播图数组
 */
export async function getBanners() {
  return await request.get("/api/banner");
}
```

### request.js - axios封装

```javascript
// src/api/request.js
import axios from "axios";
import showMessage from "../utils/showMessage";

// 创建axios实例
const ins = axios.create();

// 响应拦截器 - 统一处理响应数据
ins.interceptors.response.use(function(resp) {
  // 判断业务状态码
  if (resp.data.code !== 0) {
    showMessage({
      content: resp.data.msg,
      type: "error",
      duration: 1500,
    });
    return null;
  }
  // 返回数据部分，便于组件使用
  return resp.data.data;
});

export default ins;
```

## Home组件实现

### 模板结构

```vue
<template>
  <div class="home-container" ref="container">
    <!-- 轮播图容器 - 通过marginTop控制显示位置 -->
    <ul
      class="carousel-container"
      :style="{
        marginTop,
      }"
    >
      <li v-for="item in banners" :key="item.id">
        <CarouselItem />
      </li>
    </ul>
    
    <!-- 向上导航按钮 - 仅在非第一张时显示 -->
    <div v-show="index >= 1" @click="switchTo(index - 1)" class="icon icon-up">
      <Icon type="arrowUp" />
    </div>
    
    <!-- 向下导航按钮 - 仅在非最后一张时显示 -->
    <div
      v-show="index < banners.length - 1"
      @click="switchTo(index + 1)"
      class="icon icon-down"
    >
      <Icon type="arrowDown" />
    </div>
    
    <!-- 右侧指示器 -->
    <ul class="indicator">
      <li
        :class="{
          active: i === index,
        }"
        v-for="(item, i) in banners"
        :key="item.id"
        @click="switchTo(i)"
      ></li>
    </ul>
  </div>
</template>
```

### 脚本逻辑

```javascript
<script>
import { getBanners } from "@/api/banner";
import CarouselItem from "./Carouselitem";
import Icon from "@/components/Icon";

export default {
  name: "Home",
  components: {
    CarouselItem,
    Icon,
  },
  data() {
    return {
      banners: [],          // 轮播图数据数组
      index: 1,             // 当前显示的轮播图索引
      containerHeight: 0,   // 容器高度
    };
  },
  
  // 组件创建时获取轮播图数据
  async created() {
    this.banners = await getBanners();
  },
  
  // 组件挂载后获取容器高度
  mounted() {
    this.containerHeight = this.$refs.container.clientHeight;
  },
  
  computed: {
    // 计算marginTop实现轮播图切换效果
    marginTop() {
      return -this.index * this.containerHeight + "px";
    },
  },
  
  methods: {
    // 切换到指定索引的轮播图
    switchTo(i) {
      this.index = i;
    },
  },
};
</script>
```

### 样式实现

```less
<style lang="less" scoped>
@import "~@/styles/mixin.less";
@import "~@/styles/var.less";

.home-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
  ul {
    margin: 0;
    list-style: none;
    padding: 0;
  }
}

.carousel-container {
  width: 100%;
  height: 100%;
  transition: 500ms;  // 添加过渡动画效果
  li {
    width: 100%;
    height: 100%;
  }
}

// 上下导航图标样式
.icon {
  .self-center();  // 使用mixin实现绝对居中
  font-size: 30px;
  @gap: 25px;
  color: @gray;
  cursor: pointer;
  transform: translateX(-50%);
  
  &.icon-up {
    top: @gap;
    animation: jump-up 2s infinite;  // 向上跳动动画
  }
  
  &.icon-down {
    top: auto;
    bottom: @gap;
    animation: jump-down 2s infinite;  // 向下跳动动画
  }
  
  // 向上跳动动画关键帧
  @jump: 5px;
  @keyframes jump-up {
    0% { transform: translate(-50%, @jump); }
    50% { transform: translate(-50%, -@jump); }
    100% { transform: translate(-50%, @jump); }
  }
  
  // 向下跳动动画关键帧
  @keyframes jump-down {
    0% { transform: translate(-50%, -@jump); }
    50% { transform: translate(-50%, @jump); }
    100% { transform: translate(-50%, -@jump); }
  }
}

// 右侧指示器样式
.indicator {
  .self-center();
  transform: translateY(-50%);
  left: auto;
  right: 20px;
  
  li {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: @words;
    cursor: pointer;
    margin-bottom: 10px;
    border: 1px solid #fff;
    box-sizing: border-box;
    
    &.active {
      background: #fff;  // 当前项高亮显示
    }
  }
}
</style>
```

## CarouselItem组件实现

### 基础版本

```vue
<template>
  <div class="carousel-item-container">
    CarouselItem
  </div>
</template>

<script>
export default {
  name: "CarouselItem",
};
</script>

<style lang="less" scoped>
@import "~@/styles/var.less";

.carousel-item-container {
  background: @dark;
  width: 100%;
  height: 100%;
  color: #fff;
}
</style>
```

## 核心知识点

### 1. 动态样式绑定

Vue支持通过`:style`绑定动态样式对象：

```vue
<div :style="{ marginTop: computedValue }">
```

### 2. 条件渲染指令

`v-show`用于控制元素显示/隐藏（CSS display方式）：

```vue
<div v-show="index >= 1">仅在index>=1时显示</div>
```

### 3. 列表渲染

`v-for`用于遍历数组渲染列表：

```vue
<li v-for="(item, index) in items" :key="item.id">
  {{ index }}: {{ item.name }}
</li>
```

### 4. 计算属性

computed属性用于基于响应式数据计算派生值：

```javascript
computed: {
  marginTop() {
    return -this.index * this.containerHeight + "px";
  }
}
```

### 5. 生命周期钩子

- `created()`: 组件实例创建完成，可访问data、computed
- `mounted()`: 组件DOM挂载完成，可访问$refs

### 6. 过渡动画

CSS transition实现平滑过渡效果：

```css
.carousel-container {
  transition: 500ms;  /* 500毫秒过渡时间 */
}
```

### 7. 关键帧动画

CSS @keyframes定义复杂动画序列：

```css
@keyframes jump-up {
  0% { transform: translate(-50%, 5px); }
  50% { transform: translate(-50%, -5px); }
  100% { transform: translate(-50%, 5px); }
}
```

## 实现原理

### 轮播图切换原理

1. **垂直排列**：所有轮播图在ul中垂直排列
2. **容器高度**：每个li高度等于容器高度（100%）
3. **视口限制**：父容器设置overflow: hidden只显示一张
4. **位置控制**：通过改变ul的marginTop实现切换
5. **过渡动画**：CSS transition实现平滑过渡效果

```
容器视口（overflow: hidden）
┌─────────────────┐
│ 显示区域        │
└─────────────────┘

实际内容（通过marginTop改变位置）
┌─────────────────┐
│  轮播图1        │
├─────────────────┤
│  轮播图2        │ ← 当前显示
├─────────────────┤
│  轮播图3        │
└─────────────────┘
```

## 拓展思考

1. 如何添加鼠标滚轮事件支持？
2. 如何实现自动播放功能？
3. 如何优化移动端体验？
4. 如何添加轮播图内容的渐入动画？

## 下一步

在下一章节中，我们将完善CarouselItem组件的内容显示，包括：
- 背景图片加载和显示
- 文字内容排版
- 渐入动画效果
- 响应式布局适配