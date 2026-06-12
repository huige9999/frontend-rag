# 23. 开发文章详情页-part1

## 章节概述

本章节将带领大家开发博客文章详情页面，这是博客系统中的核心功能之一。详情页需要展示文章的完整内容、元数据（标题、发布时间、分类等），并提供良好的阅读体验。

## 学习目标

- 理解文章详情页的功能需求
- 掌握Vue路由参数的获取方式
- 学习如何通过API获取文章详情数据
- 实现文章详情页的基本布局结构

## 核心知识点

### 1. 路由参数获取

在Vue Router中，我们需要从URL中获取文章ID来请求对应的文章详情。

```javascript
// 获取路由参数
const route = useRoute();
const blogId = route.params.id;
```

### 2. 文章详情数据结构

一个完整的文章详情通常包含以下信息：

- 文章标题
- 文章内容
- 发布时间
- 分类信息
- 作者信息
- 浏览量
- 点赞数

### 3. API数据请求

使用axios或fetch获取文章详情数据：

```javascript
// 获取文章详情
async function fetchBlogDetail() {
  try {
    const response = await axios.get(`/api/blog/${blogId}`);
    blogDetail.value = response.data;
  } catch (error) {
    console.error('获取文章详情失败:', error);
  }
}
```

## 代码实现

### 基础组件结构

```vue
<template>
  <h1>博客详情</h1>
</template>

<script>
export default {};
</script>

<style></style>
```

**代码说明：**
- 这是一个基础的Vue组件模板
- `<template>` 部分定义了组件的HTML结构
- `<script>` 部分包含组件的逻辑代码
- `<style>` 部分用于编写组件的样式

### 完整的文章详情组件

```vue
<template>
  <div class="blog-detail-container">
    <!-- 文章头部信息 -->
    <div class="blog-header">
      <h1 class="blog-title">{{ blogDetail.title }}</h1>
      <div class="blog-meta">
        <span class="meta-item">
          <i class="icon-calendar"></i>
          {{ formatDate(blogDetail.createDate) }}
        </span>
        <span class="meta-item">
          <i class="icon-eye"></i>
          {{ blogDetail.scanNumber }} 浏览
        </span>
        <span class="meta-item">
          <i class="icon-comment"></i>
          {{ blogDetail.commentNumber }} 评论
        </span>
      </div>
      <div class="blog-category">
        <span>{{ blogDetail.category?.name }}</span>
      </div>
    </div>

    <!-- 文章内容 -->
    <div class="blog-content" v-html="blogDetail.htmlContent"></div>

    <!-- 文章底部操作 -->
    <div class="blog-footer">
      <button class="btn-like" @click="handleLike">
        <i class="icon-thumbs-up"></i>
        点赞 {{ blogDetail.likeNumber }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { getBlogDetail } from '@/api/blog';
import { formatDate } from '@/utils/format';

// 获取路由实例
const route = useRoute();

// 响应式数据
const blogDetail = ref({
  title: '',
  content: '',
  htmlContent: '',
  createDate: '',
  scanNumber: 0,
  commentNumber: 0,
  likeNumber: 0,
  category: null
});

// 获取文章详情
const fetchBlogDetail = async () => {
  try {
    const blogId = route.params.id;
    const result = await getBlogDetail(blogId);
    blogDetail.value = result.data;
  } catch (error) {
    console.error('获取文章详情失败:', error);
    // 可以添加错误提示组件
  }
};

// 点赞处理
const handleLike = () => {
  // TODO: 实现点赞逻辑
  console.log('点赞功能待实现');
};

// 组件挂载时获取数据
onMounted(() => {
  fetchBlogDetail();
});
</script>

<style scoped>
.blog-detail-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.blog-header {
  margin-bottom: 30px;
  padding-bottom: 20px;
  border-bottom: 1px solid #eee;
}

.blog-title {
  font-size: 28px;
  font-weight: bold;
  margin-bottom: 15px;
  line-height: 1.4;
}

.blog-meta {
  display: flex;
  gap: 20px;
  margin-bottom: 10px;
  color: #666;
  font-size: 14px;
}

.meta-item {
  display: flex;
  align-items: center;
  gap: 5px;
}

.blog-category {
  margin-top: 10px;
}

.blog-category span {
  display: inline-block;
  padding: 4px 12px;
  background-color: #f0f0f0;
  border-radius: 4px;
  font-size: 14px;
  color: #333;
}

.blog-content {
  line-height: 1.8;
  font-size: 16px;
  color: #333;
}

.blog-content img {
  max-width: 100%;
  height: auto;
}

.blog-footer {
  margin-top: 40px;
  padding-top: 20px;
  border-top: 1px solid #eee;
}

.btn-like {
  padding: 10px 20px;
  background-color: #409eff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-like:hover {
  background-color: #66b1ff;
}
</style>
```

**代码说明：**
1. **模板部分**：使用语义化HTML结构，包含文章头部、内容和底部操作区域
2. **脚本部分**：
   - 使用Composition API (`<script setup>`)
   - 通过 `useRoute` 获取路由参数
   - 使用 `ref` 创建响应式数据
   - 在 `onMounted` 钩子中获取文章详情
3. **样式部分**：提供了清晰、美观的样式，包含响应式布局

## 路由配置

确保路由配置正确：

```javascript
// router/index.js
{
  path: '/blog/:id',
  name: 'BlogDetail',
  component: () => import('@/views/Blog/Detail.vue'),
  meta: {
    title: '文章详情'
  }
}
```

**注意事项：**
- 使用动态路由参数 `:id` 来匹配不同的文章
- 使用懒加载方式导入组件
- 可以设置页面标题等元信息

## API接口定义

```javascript
// api/blog.js
import axios from '@/utils/axios';

/**
 * 获取文章详情
 * @param {string|number} id - 文章ID
 * @returns {Promise} 文章详情数据
 */
export function getBlogDetail(id) {
  return axios.get(`/api/blog/${id}`);
}

/**
 * 文章点赞
 * @param {string|number} id - 文章ID
 * @returns {Promise}
 */
export function likeBlog(id) {
  return axios.post(`/api/blog/${id}/like`);
}
```

## 数据流转过程

1. **用户访问详情页** → URL: `/blog/123`
2. **路由匹配** → 解析出文章ID: `123`
3. **组件初始化** → 触发 `onMounted` 钩子
4. **发起API请求** → 调用 `getBlogDetail(123)`
5. **接收响应数据** → 更新 `blogDetail` 响应式数据
6. **页面自动渲染** → Vue响应式系统更新DOM

## 常见问题与解决方案

### 1. 数据未加载完成显示空白

**解决方案：添加加载状态**

```javascript
const isLoading = ref(true);

const fetchBlogDetail = async () => {
  isLoading.value = true;
  try {
    const result = await getBlogDetail(route.params.id);
    blogDetail.value = result.data;
  } catch (error) {
    // 错误处理
  } finally {
    isLoading.value = false;
  }
};
```

```vue
<div v-if="isLoading" class="loading">加载中...</div>
<div v-else class="blog-detail-container">
  <!-- 文章内容 -->
</div>
```

### 2. 文章不存在处理

```javascript
const fetchBlogDetail = async () => {
  try {
    const result = await getBlogDetail(route.params.id);
    if (!result.data) {
      // 跳转到404页面
      router.push('/404');
      return;
    }
    blogDetail.value = result.data;
  } catch (error) {
    if (error.response?.status === 404) {
      router.push('/404');
    } else {
      // 其他错误处理
    }
  }
};
```

### 3. Markdown内容渲染

如果文章内容是Markdown格式，需要使用markdown-it或marked等库进行解析：

```javascript
import MarkdownIt from 'markdown-it';

const md = new MarkdownIt();
const htmlContent = computed(() => {
  return md.render(blogDetail.value.content);
});
```

## 下一步计划

在下一章节中，我们将继续完善文章详情页：

- [ ] 添加文章评论区功能
- [ ] 实现文章点赞交互
- [ ] 添加相关文章推荐
- [ ] 优化移动端显示效果
- [ ] 添加代码高亮功能

## 总结

本章节完成了文章详情页的基础框架搭建：

✅ 理解了路由参数的获取方式  
✅ 掌握了文章详情数据结构  
✅ 实现了基础的文章详情展示  
✅ 学会了处理数据加载和错误状态  

## 练习题

1. 尝试添加一个"返回文章列表"的按钮
2. 实现文章浏览量的自动增加
3. 添加一个骨架屏加载效果
4. 思考如何实现文章的分页显示（上一篇/下一篇）

---
**章节代码位置**：`d:/workpace-git/frontend-training/08. vue从入门到实战/23. 开发文章详情页-part1/课堂代码/my-site/src/views/Blog/Detail.vue`
