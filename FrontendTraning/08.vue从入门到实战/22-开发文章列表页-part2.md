# 22. 开发文章列表页-part2

## 章节概述

本章节继续开发文章列表页，重点讲解动态路由、编程式导航、watch监听等核心概念，以及如何处理分类切换和分页功能。

## 知识点说明

### 1. 动态路由

#### 场景需求

我们希望下面的地址都能够匹配到`Blog`组件：

- `/article`，显示全部文章
- `/article/cate/1`，显示分类`id`为`1`的文章
- `/article/cate/3`，显示分类`id`为`3`的文章
- ...

#### 路由配置

第一种情况很简单，只需要将一个固定的地址匹配到`Blog`组件即可：

```js
{
  path: "/article",
  name: "Blog",
  component: Blog
}
```

但后面的情况则不同：匹配到`Blog`组件的地址中，有一部分是动态变化的，则需要使用一种特殊的表达方式：

```js
{
  path: "/article/cate/:categoryId",
  name: "CategoryBlog",
  component: Blog
}
```

在地址中使用`:xxx`，来表达这一部分的内容是变化的，在`vue-router`中，将变化的这一部分称之为`params`，可以在`vue`组件中通过`this.$route.params`来获取

```js
// 访问 /article/cate/3
this.$route.params // { categoryId: "3" }
// 访问 /article/cate/1
this.$route.params // { categoryId: "1" }
```

### 2. 动态路由的导航

#### 使用router-link

```vue
<!-- 直接路径 -->
<router-link to="/article/cate/3">to article of category 3</router-link>

<!-- 命名路由 -->
<router-link :to="{
   name: 'CategoryBlog',
   params: {
       categoryId: 3           
   }                    
}">to article of category 3</router-link>
```

### 3. 编程式导航

除了使用`<RouterLink>`超链接导航外，`vue-router`还允许在代码中跳转页面

```js
// 普通跳转
this.$router.push("跳转地址");

// 命名路由跳转
this.$router.push({
  name:"Blog"
})

// 带参数跳转
this.$router.push({
  name: "CategoryBlog",
  query: {
    page: 1,
    limit: 10
  },
  params: {
    categoryId: 3
  }
});

// 回退。类似于 history.go
this.$router.go(-1);
```

### 4. watch监听

利用`watch`配置，可以直接观察某个数据的变化，变化时可以做一些处理

```js
export default {
  // ... 其他配置
  watch: {
    // 观察 this.$route 的变化，变化后，会调用该函数
    $route(newVal, oldVal){
      // newVal：this.$route 新的值，等同 this.$route
      // oldVal：this.$route 旧的值
    },
    // 完整写法
    $route: {
      handler(newVal, oldVal){
        // 路由变化时执行
      },
      deep: false, // 是否监听该数据内部属性的变化，默认 false
      immediate: false // 是否立即执行一次 handler，默认 false
    },
    // 观察 this.$route.params 的变化，变化后，会调用该函数
    ["$route.params"](newVal, oldVal){
      // newVal：this.$route.params 新的值，等同 this.$route.params
      // oldVal：this.$route.params 旧的值
    },
    // 完整写法
    ["$route.params"]: {
      handler(newVal, oldVal){
        // params变化时执行
      },
      deep: false, // 是否监听该数据内部属性的变化，默认 false
      immediate: false // 是否立即执行一次 handler，默认 false
    }
  }
}
```

## 代码实现

### 1. Blog主组件

```vue
<template>
  <Layout>
    <BlogList />
    <template #right>
      <BlogCategory />
    </template>
  </Layout>
</template>

<script>
import Layout from "@/components/Layout";
import BlogList from "./components/BlogList";
import BlogCategory from "./components/BlogCategory";

export default {
  components: {
    Layout,
    BlogList,
    BlogCategory,
  },
};
</script>
```

### 2. BlogList组件

```vue
<template>
  <div class="blog-list-container" ref="container" v-loading="isLoading">
    <ul>
      <li v-for="item in data.rows" :key="item.id">
        <div class="thumb" v-if="item.thumb">
          <a href="">
            <img :src="item.thumb" :alt="item.title" :title="item.title" />
          </a>
        </div>
        <div class="main">
          <a href="">
            <h2>{{ item.title }}</h2>
          </a>
          <div class="aside">
            <span>日期：{{ formatDate(item.createDate) }}</span>
            <span>浏览：{{ item.scanNumber }}</span>
            <span>评论：{{ item.commentNumber }}</span>
            <a href="/article/cate/8" class="">{{ item.category.name }}</a>
          </div>
          <div class="desc">
            {{ item.description }}
          </div>
        </div>
      </li>
    </ul>
    <!-- 分页组件 -->
    <Pager
      v-if="data.total"
      :current="routeInfo.page"
      :total="data.total"
      :limit="routeInfo.limit"
      :visibleNumber="10"
      @pageChange="handlePageChange"
    />
  </div>
</template>

<script>
import Pager from "@/components/Pager";
import fetchData from "@/mixins/fetchData.js";
import { getBlogs } from "@/api/blog.js";
import { formatDate } from "@/utils";

export default {
  mixins: [fetchData({})], // 使用混入获取数据
  components: {
    Pager,
  },
  computed: {
    // 获取路由信息
    routeInfo() {
      const categoryId = +this.$route.params.categoryId || -1; // 获取分类ID，默认-1表示全部
      const page = +this.$route.query.page || 1; // 获取当前页码，默认第1页
      const limit = +this.$route.query.limit || 10; // 获取每页数量，默认10条
      return {
        categoryId,
        page,
        limit,
      };
    },
  },
  methods: {
    formatDate, // 日期格式化方法
    // 重写混入中的fetchData方法
    async fetchData() {
      return await getBlogs(
        this.routeInfo.page,
        this.routeInfo.limit,
        this.routeInfo.categoryId
      );
    },
    // 处理分页变化
    handlePageChange(newPage) {
      const query = {
        page: newPage,
        limit: this.routeInfo.limit,
      };
      // 根据当前是否选择分类，跳转到不同的路由
      if (this.routeInfo.categoryId === -1) {
        // 当前没有分类，跳转到全部文章页
        this.$router.push({
          name: "Blog",
          query,
        });
      } else {
        // 当前有分类，跳转到分类文章页
        this.$router.push({
          name: "CategoryBlog",
          query,
          params: {
            categoryId: this.routeInfo.categoryId,
          },
        });
      }
    },
  },
  watch: {
    // 监听路由变化
    async $route() {
      this.isLoading = true;
      this.$refs.container.scrollTop = 0; // 滚动条回到顶部
      this.data = await this.fetchData(); // 重新获取数据
      this.isLoading = false;
    },
  },
};
</script>

<style scoped lang="less">
@import "~@/styles/var.less";

.blog-list-container {
  line-height: 1.7;
  position: relative;
  padding: 20px;
  overflow-y: scroll;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  scroll-behavior: smooth;

  ul {
    list-style: none;
    margin: 0;
    padding: 0;
  }
}

li {
  display: flex;
  padding: 15px 0;
  border-bottom: 1px solid @gray;

  .thumb {
    flex: 0 0 auto;
    margin-right: 15px;

    img {
      display: block;
      max-width: 200px;
      border-radius: 5px;
    }
  }

  .main {
    flex: 1 1 auto;

    h2 {
      margin: 0;
    }
  }

  .aside {
    font-size: 12px;
    color: @gray;

    span {
      margin-right: 15px;
    }
  }

  .desc {
    margin: 15px 0;
    font-size: 14px;
  }
}
</style>
```

### 3. BlogCategory组件

```vue
<template>
  <div class="blog-category-container" v-loading="isLoading">
    <h2>文章分类</h2>
    <!-- 使用RightList组件展示分类列表 -->
    <RightList :list="list" @select="handleSelect" />
  </div>
</template>

<script>
import RightList from "./RightList";
import fetchData from "@/mixins/fetchData.js";
import { getBlogCategories } from "@/api/blog.js";

export default {
  mixins: [fetchData([])], // 使用混入获取数据，默认空数组
  components: {
    RightList,
  },
  computed: {
    // 获取当前选中的分类ID
    categoryId() {
      return +this.$route.params.categoryId || -1;
    },
    // 获取每页显示数量
    limit() {
      return +this.$route.query.limit || 10;
    },
    // 处理分类列表数据
    list() {
      // 计算文章总数
      const totalArticleCount = this.data.reduce(
        (a, b) => a + b.articleCount,
        0
      );

      // 在列表前面添加"全部"选项
      const result = [
        { name: "全部", id: -1, articleCount: totalArticleCount },
        ...this.data,
      ];

      // 处理每个分类项，添加选中状态和显示文本
      return result.map((it) => ({
        ...it,
        isSelect: it.id === this.categoryId, // 判断是否选中
        aside: `${it.articleCount}篇`, // 显示文章数量
      }));
    },
  },
  methods: {
    // 重写混入中的fetchData方法
    async fetchData() {
      return await getBlogCategories();
    },
    // 处理分类选择
    handleSelect(item) {
      const query = {
        page: 1, // 重置页码为第1页
        limit: this.limit,
      };

      // 根据选择的分类ID进行路由跳转
      if (item.id === -1) {
        // 选择"全部"，跳转到全部文章页
        this.$router.push({
          name: "Blog",
          query,
        });
      } else {
        // 选择具体分类，跳转到分类文章页
        this.$router.push({
          name: "CategoryBlog",
          query,
          params: {
            categoryId: item.id,
          },
        });
      }
    },
  },
};
</script>

<style scoped lang="less">
.blog-category-container {
  width: 300px;
  box-sizing: border-box;
  padding: 20px;
  position: relative;
  height: 100%;
  overflow-y: auto;

  h2 {
    font-weight: bold;
    letter-spacing: 2px;
    font-size: 1em;
    margin: 0;
  }
}
</style>
```

### 4. RightList组件

```vue
<template>
  <ul class="right-list-container">
    <li v-for="(item, i) in list" :key="i">
      <!-- 分类名称，可点击 -->
      <span @click="handleClick(item)" :class="{ active: item.isSelect }">
        {{ item.name }}
      </span>
      <!-- 附加信息（如文章数量），可点击 -->
      <span
        v-if="item.aside"
        @click="handleClick(item)"
        class="aside"
        :class="{ active: item.isSelect }"
      >
        {{ item.aside }}
      </span>
      <!-- 递归调用自身，支持多级分类 -->
      <RightList :list="item.children" @select="handleClick" />
    </li>
  </ul>
</template>

<script>
export default {
  name: "RightList",
  props: {
    // 列表数据格式：
    // [ {name:"xxx", isSelect:true, children:[ {name:"yyy", isSelect: false} ] } ]
    list: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    // 处理点击事件
    handleClick(item) {
      // 只有未选中的项才触发选择事件
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

  // 嵌套的列表容器
  .right-list-container {
    margin-left: 1em;
  }

  li {
    min-height: 40px;
    line-height: 40px;
    font-size: 14px;
    cursor: pointer;

    // 选中状态的样式
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

## 核心逻辑说明

### 1. 路由跳转逻辑

![路由跳转流程](http://mdrs.yuanjin.tech/img/20210107140253.png)

### 2. 组件逻辑

#### BlogList组件

![BlogList组件流程](http://mdrs.yuanjin.tech/img/20210107153623.png)

#### BlogCategory组件

![BlogCategory组件流程](http://mdrs.yuanjin.tech/img/20210107154531.png)

### 3. 数据流转

1. **初始化阶段**
   - BlogList和BlogCategory组件通过fetchData混入获取初始数据
   - 从路由中提取分类ID、页码、每页数量等参数
   - 调用API获取对应数据

2. **分类切换**
   - 用户点击BlogCategory中的分类项
   - handleSelect方法通过编程式导航跳转到新路由
   - 路由变化触发watch监听器
   - BlogList重新获取数据并更新视图

3. **分页切换**
   - 用户点击分页组件的页码
   - handlePageChange方法根据当前分类状态跳转到新路由
   - 路由变化触发watch监听器
   - BlogList重新获取数据并更新视图

## 技术要点总结

### 1. 动态路由参数的使用
- 通过`$route.params`获取动态参数
- 通过`$route.query`获取查询参数
- 注意类型转换（字符串转数字）

### 2. 编程式导航的应用
- 使用`$router.push`进行路由跳转
- 支持命名路由和参数传递
- 结合params和query实现复杂跳转

### 3. watch监听路由变化
- 监听`$route`对象响应路由变化
- 在路由变化时重新获取数据
- 添加滚动条回到顶部的用户体验优化

### 4. 组件通信与复用
- RightList组件支持递归调用处理多级分类
- 通过props和events实现父子组件通信
- 混入(mixins)实现逻辑复用

### 5. 计算属性的应用
- 使用计算属性处理数据格式转换
- 实现选中状态的动态判断
- 添加"全部"选项和文章总数统计

## 实践建议

1. **路由设计**
   - 合理规划动态路由和静态路由
   - 考虑URL的可读性和SEO友好性

2. **数据获取**
   - 使用混入封装通用数据获取逻辑
   - 在watch中处理路由变化引起的数据更新

3. **用户体验**
   - 添加loading状态提升用户体验
   - 分页切换时滚动条回到顶部
   - 选中状态的视觉反馈

4. **代码组织**
   - 将复杂逻辑拆分为多个计算属性
   - 方法命名清晰，注释完整
   - 样式使用作用域scoped避免污染

## 课后练习

1. 实现文章搜索功能
2. 添加文章排序功能（按时间、浏览量等）
3. 实现面包屑导航
4. 添加文章列表缓存功能
5. 实现文章列表的懒加载