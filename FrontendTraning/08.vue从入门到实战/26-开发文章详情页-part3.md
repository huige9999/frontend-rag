# 26. 开发文章详情页-part3

## 本节目标

完成文章详情页的开发，包括：
- 文章详情展示
- 目录导航功能
- 评论功能集成
- 页面整体布局

## 核心组件结构

```
Blog/
├── index.vue           # 文章列表页入口
├── Detail.vue          # 文章详情页入口
└── components/
    ├── BlogDetail.vue     # 文章详情内容组件
    ├── BlogTOC.vue        # 文章目录组件
    ├── BlogComment.vue    # 文章评论组件
    └── RightList.vue      # 右侧通用列表组件
```

## 1. 文章详情页入口 (Detail.vue)

### 文件位置
`src/views/Blog/Detail.vue`

### 核心功能
- 整合文章详情、目录和评论组件
- 使用 Layout 组件实现左右布局
- 集成数据获取和加载状态

### 代码实现

```vue
<template>
  <Layout>
    <!-- 左侧主内容区：文章详情和评论 -->
    <div class="main-container" v-loading="isLoading">
      <BlogDetail :blog="data" v-if="data" />
      <BlogComment v-if="!isLoading" />
    </div>
    
    <!-- 右侧：文章目录 -->
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
import BlogComment from "./components/BlogComment";

export default {
  components: {
    Layout,
    BlogDetail,
    BlogTOC,
    BlogComment,
  },
  // 使用 fetchData mixin 处理数据获取和加载状态
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
  scroll-behavior: smooth; // 平滑滚动
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

### 技术要点

1. **Layout 组件插槽**：使用 `#right` 具名插槽实现右侧内容
2. **数据获取**：通过 mixin 统一处理异步数据和加载状态
3. **条件渲染**：使用 `v-if` 和 `v-loading` 控制组件显示和加载状态
4. **路由参数**：通过 `this.$route.params.id` 获取文章 ID

## 2. 文章详情组件 (BlogDetail.vue)

### 文件位置
`src/views/Blog/components/BlogDetail.vue`

### 核心功能
- 显示文章标题、日期、浏览量等信息
- 渲染 Markdown 内容
- 样式美化

### 代码实现

```vue
<template>
  <div class="blog-detail-container">
    <!-- 文章标题 -->
    <h1>{{ blog.title }}</h1>
    
    <!-- 文章元信息 -->
    <div class="aside">
      <span>日期: {{ formatDate(blog.createDate) }}</span>
      <span>浏览: {{ blog.scanNumber }}</span>
      <a href="#data-form-container">评论: {{ blog.commentNumber }}</a>
      <a href="">{{ blog.category.name }}</a>
    </div>
    
    <!-- Markdown 内容渲染 -->
    <div v-html="blog.htmlContent" class="markdown-body"></div>
  </div>
</template>

<script>
import { formatDate } from "@/utils";
import "highlight.js/styles/github.css";
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

### 技术要点

1. **v-html 指令**：渲染 HTML 内容（从 Markdown 转换而来）
2. **样式导入**：导入 highlight.js 和 markdown 样式
3. **工具函数**：使用 formatDate 格式化日期
4. **props 验证**：使用 required 确保必传数据

## 3. 文章目录组件 (BlogTOC.vue)

### 文件位置
`src/views/Blog/components/BlogTOC.vue`

### 核心功能
- 显示文章目录结构
- 点击目录项跳转到对应位置
- 支持嵌套目录

### 代码实现

```vue
<template>
  <div class="blog-toc-container">
    <h2>目录</h2>
    <!-- 使用通用列表组件渲染目录 -->
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
      location.hash = item.anchor; // 设置页面 hash 实现跳转
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

### 技术要点

1. **Hash 跳转**：通过 `location.hash` 实现页面内跳转
2. **组件复用**：使用通用 RightList 组件
3. **事件传递**：通过 `@select` 监听列表项点击

## 4. 评论组件 (BlogComment.vue)

### 文件位置
`src/views/Blog/components/BlogComment.vue`

### 核心功能
- 显示评论列表
- 支持提交新评论
- 分页加载评论

### 代码实现

```vue
<template>
  <div class="blog-comment-container">
    <MessageArea
      title="评论列表"
      :subTitle="`(${data.total})`"
      :list="data.rows"
      :isListLoading="isLoading"
      @submit="handleSubmit"
    />
  </div>
</template>

<script>
import MessageArea from "@/components/MessageArea";
import fetchData from "@/mixins/fetchData.js";
import { getComments, postComment } from "@/api/blog.js";

export default {
  mixins: [fetchData({ total: 0, rows: [] })], // 默认数据
  components: {
    MessageArea,
  },
  data() {
    return {
      page: 1,      // 当前页码
      limit: 10,    // 每页条数
    };
  },
  methods: {
    // 获取评论列表
    async fetchData() {
      return await getComments(
        this.$route.params.id, 
        this.page, 
        this.limit
      );
    },
    
    // 处理评论提交
    async handleSubmit(formData, callback) {
      const resp = await postComment({
        blogId: this.$route.params.id,
        ...formData,
      });
      
      // 将新评论添加到列表开头
      this.data.rows.unshift(resp);
      this.data.total++;
      
      // 通知子组件处理完成
      callback("评论成功");
    },
  },
};
</script>

<style scoped lang="less">
.blog-comment-container {
  margin: 30px 0;
}
</style>
```

### 技术要点

1. **Mixin 使用**：使用 fetchData mixin 处理数据和加载状态
2. **默认数据**：在 mixin 中设置默认数据结构
3. **回调函数**：通过 callback 通知子组件处理完成
4. **数据更新**：使用 unshift 将新评论添加到列表开头

## 5. 通用列表组件 (RightList.vue)

### 文件位置
`src/views/Blog/components/RightList.vue`

### 核心功能
- 递归渲染嵌套列表
- 支持选中状态显示
- 支持附加信息显示

### 代码实现

```vue
<template>
  <ul class="right-list-container">
    <li v-for="(item, i) in list" :key="i">
      <!-- 主标题 -->
      <span 
        @click="handleClick(item)" 
        :class="{ active: item.isSelect }"
      >
        {{ item.name }}
      </span>
      
      <!-- 附加信息（如评论数） -->
      <span
        v-if="item.aside"
        @click="handleClick(item)"
        class="aside"
        :class="{ active: item.isSelect }"
      >
        {{ item.aside }}
      </span>
      
      <!-- 递归渲染子列表 -->
      <RightList :list="item.children" @select="handleClick" />
    </li>
  </ul>
</template>

<script>
export default {
  name: "RightList",
  props: {
    list: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    handleClick(item) {
      // 只有未选中时才触发事件
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
  
  // 嵌套列表缩进
  .right-list-container {
    margin-left: 1em;
  }
  
  li {
    min-height: 40px;
    line-height: 40px;
    font-size: 14px;
    cursor: pointer;
    
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

### 技术要点

1. **递归组件**：组件内部调用自身实现嵌套列表
2. **条件样式**：使用 `:class` 动态绑定样式
3. **事件冒泡**：通过 `$emit` 向父组件传递事件
4. **数据结构**：支持 `{name, aside, isSelect, children}` 结构

## 数据流程

```
用户访问 /blog/:id
    ↓
Detail.vue 组件加载
    ↓
fetchData mixin 调用 getBlog API
    ↓
获取文章数据（包含 toc 目录）
    ↓
传递给子组件：
  - BlogDetail 显示文章内容
  - BlogTOC 显示目录
  - BlogComment 加载评论列表
```

## 关键 API

### getBlog
获取文章详情，返回数据包含：
- `title`: 文章标题
- `htmlContent`: Markdown 转换后的 HTML
- `toc`: 目录结构数组
- `createDate`: 创建日期
- `scanNumber`: 浏览量
- `commentNumber`: 评论数
- `category`: 分类信息

### getComments
获取评论列表，参数：
- `blogId`: 文章 ID
- `page`: 页码
- `limit`: 每页条数

### postComment
提交评论，参数：
- `blogId`: 文章 ID
- `nickname`: 昵称
- `content`: 评论内容

## 样式处理

1. **Markdown 样式**：导入 `@/styles/markdown.css` 统一 Markdown 渲染样式
2. **代码高亮**：使用 `highlight.js` 的 GitHub 主题
3. **变量定义**：使用 `@/styles/var.less` 定义颜色等变量
4. **作用域样式**：使用 `scoped` 避免样式污染

## 总结

本节完成了文章详情页的核心功能：

1. **组件化设计**：将详情页拆分为多个功能组件
2. **数据管理**：使用 mixin 统一处理异步数据和加载状态
3. **交互实现**：目录跳转、评论提交等用户交互
4. **样式优化**：统一的样式规范和主题变量

通过这些组件的组合，构建了一个功能完整的文章详情页面。
