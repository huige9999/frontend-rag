# 24. 开发文章详情页-part2

## 课程目标

本章节将继续完善文章详情页的功能，主要包括：
- 完善文章详情页面的数据展示
- 实现文章目录（TOC）组件
- 优化右侧列表组件的复用性

## 知识点

### 1. 文章详情页整体架构

文章详情页采用左右布局结构：
- 左侧：显示文章的详细内容
- 右侧：显示文章目录、分类等信息

### 2. BlogDetail 组件

该组件负责显示文章的详细信息，包括标题、发布日期、浏览量、评论数、分类和文章内容。

**关键技术点：**

#### v-html 指令的使用
由于文章内容是原始 HTML 格式，需要使用 `v-html` 指令来渲染：

```vue
<div v-html="blog.htmlContent" class="markdown-body"></div>
```

#### 引入 Markdown 样式
文章内容需要 Markdown 的样式，引入专门的 CSS 文件：

```javascript
import "@/styles/markdown.css";
```

#### 引入代码高亮样式
使用 highlight.js 提供的样式来美化代码块：

```javascript
import "highlight.js/styles/github.css";
```

**完整代码示例：**

```vue
<template>
  <div class="blog-detail-container">
    <!-- 文章标题 -->
    <h1>{{ blog.title }}</h1>
    
    <!-- 文章元信息 -->
    <div class="aside">
      <span>日期: {{ formatDate(blog.createDate) }}</span>
      <span>浏览: {{ blog.scanNumber }}</span>
      <a href="">评论: {{ blog.commentNumber }}</a>
      <a href="">{{ blog.category.name }}</a>
    </div>
    
    <!-- 文章内容 - 使用 v-html 渲染 HTML -->
    <div v-html="blog.htmlContent" class="markdown-body"></div>
  </div>
</template>

<script>
import { formatDate } from "@/utils";
// 引入代码高亮样式
import "highlight.js/styles/github.css";
// 引入 markdown 样式
import "@/styles/markdown.css";

export default {
  props: {
    blog: {
      type: Object,
      required: true,
    },
  },
  methods: {
    formatDate,
  },
};
</script>

<style scoped lang="less">
@import "~@/styles/var.less";

.aside {
  font-size: 12px;
  color: @gray;
  span,
  a {
    margin-right: 15px;
  }
}

.markdown-body {
  margin: 2em 0;
}
</style>
```

### 3. BlogTOC 组件

该组件负责显示文章的目录结构，点击目录项可以滚动到对应的章节。

**功能说明：**
- 展示文章的目录结构
- 支持点击跳转到对应章节
- 使用 RightList 组件实现递归渲染

**完整代码示例：**

```vue
<template>
  <div class="blog-toc-container">
    <h2>目录</h2>
    <!-- 使用 RightList 组件递归渲染目录 -->
    <RightList :list="toc" @select="handleSelect" />
  </div>
</template>

<script>
import RightList from "./RightList";

export default {
  components: {
    RightList,
  },
  props: {
    toc: {
      type: Array,
    },
  },
  methods: {
    // 处理目录项点击事件
    handleSelect(item) {
      // 通过锚点跳转到对应章节
      location.hash = item.anchor;
    },
  },
};
</script>

<style scoped lang="less">
.blog-toc-container {
  h2 {
    font-weight: bold;
    letter-spacing: 2px;
    font-size: 1em;
    margin: 0;
  }
}
</style>
```

### 4. RightList 通用组件

这是一个可复用的右侧列表组件，支持递归渲染嵌套结构。

**组件特性：**
- 支持递归渲染，可以显示多级目录
- 支持选中状态高亮
- 支持显示附加信息（aside）
- 通过事件向父组件传递选择事件

**完整代码示例：**

```vue
<template>
  <ul class="right-list-container">
    <li v-for="(item, i) in list" :key="i">
      <!-- 主文本 -->
      <span 
        @click="handleClick(item)" 
        :class="{ active: item.isSelect }"
      >
        {{ item.name }}
      </span>
      
      <!-- 附加信息 -->
      <span
        v-if="item.aside"
        @click="handleClick(item)"
        class="aside"
        :class="{ active: item.isSelect }"
      >
        {{ item.aside }}
      </span>
      
      <!-- 递归渲染子项 -->
      <RightList 
        :list="item.children" 
        @select="handleClick" 
      />
    </li>
  </ul>
</template>

<script>
export default {
  name: "RightList",
  props: {
    // 列表数据格式：
    // [{ 
    //   name: "xxx", 
    //   isSelect: true, 
    //   aside: "附加信息",
    //   children: [{ name: "yyy", isSelect: false }] 
    // }]
    list: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    handleClick(item) {
      // 只有未选中的项才能触发点击事件
      if (!item.isSelect) {
        this.$emit("select", item);
      }
    },
  },
};
</script>

<style scoped lang="less">
@import "~@/styles/var.less";

.right-list-container {
  list-style: none;
  padding: 0;
  
  // 子列表缩进
  .right-list-container {
    margin-left: 1em;
  }
  
  li {
    min-height: 40px;
    line-height: 40px;
    font-size: 14px;
    cursor: pointer;
    
    // 选中状态样式
    .active {
      color: @warn;
      font-weight: bold;
    }
  }
}

.aside {
  font-size: 12px;
  margin-left: 1em;
  color: @gray;
}
</style>
```

### 5. Detail 页面整合

将各个组件整合到文章详情页面中。

**完整代码示例：**

```vue
<template>
  <Layout>
    <!-- 左侧内容区域 -->
    <div class="main-container" v-loading="isLoading">
      <BlogDetail :blog="data" v-if="data" />
    </div>
    
    <!-- 右侧边栏 -->
    <template #right>
      <div class="right-container" v-loading="isLoading">
        <BlogTOC :toc="data.toc" v-if="data" />
      </div>
    </template>
  </Layout>
</template>

<script>
import fetchData from "@/mixins/fetchData";
import { getBlog } from "@/api/blog";
import Layout from "@/components/Layout";
import BlogDetail from "./components/BlogDetail";
import BlogTOC from "./components/BlogTOC";

export default {
  components: {
    Layout,
    BlogDetail,
    BlogTOC,
  },
  // 使用 fetchData mixin 处理数据加载
  mixins: [fetchData(null)],
  methods: {
    // 获取文章详情数据
    async fetchData() {
      return await getBlog(this.$route.params.id);
    },
  },
};
</script>

<style scoped lang="less">
.main-container {
  overflow-y: scroll;
  height: 100%;
  box-sizing: border-box;
  padding: 20px;
  position: relative;
  width: 100%;
  overflow-x: hidden;
  // 平滑滚动
  scroll-behavior: smooth;
}

.right-container {
  width: 300px;
  height: 100%;
  overflow-y: scroll;
  box-sizing: border-box;
  position: relative;
  padding: 20px;
}
</style>
```

## 数据流程

### 文章数据获取流程

1. **路由参数获取**：从 `$route.params.id` 获取文章 ID
2. **API 调用**：通过 `getBlog()` 方法获取文章数据
3. **数据传递**：将数据传递给 BlogDetail 和 BlogTOC 组件
4. **内容渲染**：组件根据接收到的数据渲染相应内容

### 目录跳转流程

1. **用户点击目录项**：触发 BlogTOC 的 `handleSelect` 方法
2. **设置锚点**：通过 `location.hash` 设置页面锚点
3. **页面滚动**：浏览器自动滚动到对应位置
4. **平滑滚动**：通过 CSS 的 `scroll-behavior: smooth` 实现平滑滚动效果

## 组件通信

### 父子组件通信

1. **Detail → BlogDetail**
   - 通过 props 传递文章对象：`:blog="data"`

2. **Detail → BlogTOC**
   - 通过 props 传递目录数组：`:toc="data.toc"`

3. **BlogTOC → RightList**
   - 通过 props 传递目录数据：`:list="toc"`
   - 通过事件接收选择结果：`@select="handleSelect"`

### 递归组件通信

RightList 组件通过递归调用自身实现多级目录的渲染：
- 父级 RightList 传递子级数据给子级 RightList
- 子级选择事件向上传递到最顶层，最终传递给 BlogTOC

## 样式处理

### Markdown 样式

使用专门的 markdown.css 样式文件来美化文章内容：
- 标题样式
- 列表样式
- 引用样式
- 表格样式
- 代码块样式

### 代码高亮样式

使用 highlight.js 的 GitHub 主题样式：
```javascript
import "highlight.js/styles/github.css";
```

### 自定义样式

- 使用 LESS 变量统一管理颜色和尺寸
- 使用 scoped 样式避免样式污染
- 响应式布局适配不同屏幕尺寸

## 扩展知识点

### 1. v-html 的安全注意事项

使用 v-html 渲染 HTML 内容时需要注意：
- 确保 HTML 内容来自可信来源
- 避免渲染用户提交的未经验证的 HTML
- 考虑使用 DOMPurify 等库进行 HTML 清理

### 2. 平滑滚动实现

除了使用 CSS 的 `scroll-behavior: smooth`，还可以使用 JavaScript 实现：

```javascript
// 平滑滚动到指定元素
element.scrollIntoView({ 
  behavior: 'smooth',
  block: 'start'
});
```

### 3. 递归组件的使用

递归组件要点：
- 必须给组件设置 name 属性
- 需要有明确的递归终止条件
- 注意性能问题，避免过深的递归层级

## 实践练习

1. 尝试添加文章分享功能
2. 实现文章字号大小调节
3. 添加夜间模式切换
4. 优化移动端显示效果

## 总结

本章节完成了文章详情页的核心功能，包括：
- 文章内容的完整展示
- 文章目录的生成和跳转
- 通用列表组件的设计和实现
- 样式的优化和代码高亮

重点掌握了：
- v-html 指令的正确使用
- 递归组件的设计思想
- 组件间的通信模式
- 样式的模块化管理
