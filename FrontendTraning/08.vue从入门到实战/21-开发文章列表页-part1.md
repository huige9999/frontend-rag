# 第21章 开发文章列表页-part1

## 本章目标

- 掌握文章列表页的整体布局设计
- 学会使用递归组件实现分类导航
- 了解如何集成API接口获取文章数据
- 掌握数据混入(Mixin)的使用方式
- 实现文章列表的基础功能

## 文章列表页概述

文章列表页是博客系统的核心页面之一，需要展示以下内容：

- 文章分类导航（支持多级分类）
- 文章列表展示
- 分页功能
- 加载状态处理

### 页面布局设计

```
┌─────────────────────────────────────┐
│         文章分类导航（右侧）          │
├─────────────────────────────────────┤
│                                     │
│          文章列表内容                │
│                                     │
│                                     │
└─────────────────────────────────────┘
```

## API接口封装

### 博客相关接口

首先在 `src/api/blog.js` 中封装博客相关的API接口：

```javascript
import request from "./request";

/**
 * 获取博客列表数据
 * @param {number} page - 页码，默认为1
 * @param {number} limit - 每页数量，默认为10
 * @param {number} categoryid - 分类ID，默认为-1（全部）
 * @returns {Promise} 返回博客列表数据
 */
export async function getBlogs(page = 1, limit = 10, categoryid = -1) {
  return await request.get("/api/blog", {
    params: {
      page,
      limit,
      categoryid,
    },
  });
}

/**
 * 获取博客分类
 * @returns {Promise} 返回分类列表数据
 */
export async function getBlogTypes() {
  return await request.get("/api/blogtype");
}
```

### 接口说明

1. **getBlogs函数**
   - 支持分页查询（page、limit参数）
   - 支持按分类筛选（categoryid参数）
   - 返回包含文章列表和总数的响应数据

2. **getBlogTypes函数**
   - 获取所有文章分类
   - 用于构建分类导航

## 递归组件：RightList

### 组件功能

RightList组件用于展示多级分类导航，支持：

- 递归渲染多级分类
- 选中状态高亮显示
- 点击分类触发事件

### 组件实现

```vue
<template>
  <ul class="right-list-container">
    <!-- 遍历当前层级的列表项 -->
    <li v-for="(item, i) in list" :key="i">
      <!-- 分类名称，支持点击和选中高亮 -->
      <span @click="handleClick(item)" :class="{ active: item.isSelect }">
        {{ item.name }}
      </span>
      <!-- 递归渲染子分类 -->
      <!-- 显示当前组件 -->
      <RightList :list="item.children" @select="handleClick" />
    </li>
  </ul>
</template>

<script>
export default {
  name: "RightList", // 必须设置name，递归组件需要
  props: {
    // 接收列表数据，格式：
    // [ 
    //   {name:"xxx", isSelect:true, children:[ 
    //     {name:"yyy", isSelect: false} 
    //   ]} 
    // ]
    list: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    // 处理点击事件
    handleClick(item) {
      // 向父组件传递select事件
      this.$emit("select", item);
    },
  },
};
</script>

<style scoped lang="less">
@import "~@/styles/var.less";

.right-list-container {
  list-style: none;
  padding: 0;
  
  // 子级列表缩进
  .right-list-container {
    margin-left: 1em;
  }
  
  li {
    min-height: 40px;
    line-height: 40px;
    cursor: pointer;
    
    // 选中状态样式
    .active {
      color: @warn;
      font-weight: bold;
    }
  }
}
</style>
```

### 递归组件要点

1. **必须设置name属性**
   ```javascript
   name: "RightList" // 组件在模板中引用自身需要
   ```

2. **递归终止条件**
   ```vue
   <!-- 当list为空时，v-for不会渲染，自动终止递归 -->
   <RightList :list="item.children" @select="handleClick" />
   ```

3. **事件传递**
   ```javascript
   // 子组件通过$emit向父组件传递事件
   this.$emit("select", item);
   ```

## 组件测试数据

### RightList测试组件

```vue
<template>
  <RightList :list="list" @select="handleSelect" />
</template>

<script>
import RightList from "./RightList";

export default {
  components: {
    RightList,
  },
  data() {
    return {
      // 测试数据：模拟多级分类结构
      list: [
        { name: "a", isSelect: false },
        { name: "b", isSelect: false },
        {
          name: "c",
          isSelect: true,
          children: [
            { name: "c-1", isSelect: false },
            {
              name: "c-2",
              isSelect: false,
              children: [
                { name: "c-2-1", isSelect: false },
                { name: "c-2-2", isSelect: false },
                { name: "c-2-3", isSelect: false },
                { name: "c-2-4", isSelect: false },
              ],
            },
            { name: "c-3", isSelect: false },
            { name: "c-4", isSelect: false },
          ],
        },
        { name: "d", isSelect: false },
      ],
    };
  },
  methods: {
    handleSelect(item) {
      console.log("选中分类:", item);
    },
  },
};
</script>
```

## 数据混入(Mixin)

### fetchData混入

为了简化数据获取的逻辑，创建一个通用的数据获取混入：

```javascript
// 公共的远程获取数据的代码
// 具体的组件中，需要提供一个远程获取数据的方法 fetchData
export default function(defaultDataValue = null) {
  return {
    data() {
      return {
        isLoading: true,     // 加载状态
        data: defaultDataValue, // 数据
      };
    },
    async created() {
      // 组件创建时自动调用fetchData方法
      this.data = await this.fetchData();
      this.isLoading = false;
    },
  };
}
```

### 使用Mixin

```javascript
import fetchData from "@/mixins/fetchData";

export default {
  mixins: [fetchData(null)],
  methods: {
    // 组件需要实现fetchData方法
    async fetchData() {
      // 返回获取数据的Promise
      return await getBlogs();
    }
  }
}
```

## Mock数据配置

### 博客Mock数据

```javascript
import Mock from "mockjs";
import qs from "querystring";

// 文章分类Mock数据
Mock.mock("/api/blogtype", "get", {
  code: 0,
  msg: "",
  "data|10-20": [
    {
      "id|+1": 1,
      name: "分类@id",
      "articleCount|0-300": 0,
      "order|+1": 1,
    },
  ],
});

// 文章列表Mock数据
Mock.mock(/^\/api\/blog(\?.+)?$/, "get", function(options) {
  const query = qs.parse(options.url);

  return Mock.mock({
    code: 0,
    msg: "",
    data: {
      "total|2000-3000": 0, // 文章总数
      [`rows|${query.limit || 10}`]: [ // 根据limit参数生成对应数量的文章
        {
          id: "@guid",
          title: "@ctitle",
          description: "@cparagraph(1, 10)",
          category: {
            "id|1-10": 0,
            name: "分类@id",
          },
          "scanNumber|0-3000": 0,    // 浏览数
          "commentNumber|0-300": 30, // 评论数
          thumb: Mock.Random.image("300x250", "#000", "#fff", "Random Image"),
          createDate: `@date('T')`,
        },
      ],
    },
  });
});
```

### Mock数据说明

1. **分类数据**
   - 生成10-20个随机分类
   - 包含id、名称、文章数量、排序等字段

2. **文章列表数据**
   - 支持分页参数（limit）
   - 生成随机文章数据
   - 包含标题、描述、分类、浏览数、评论数等

## Blog页面基础结构

### 初始实现

```vue
<template>
  <h1>文章</h1>
</template>

<script>
export default {};
</script>

<style></style>
```

## Axios拦截器配置

### request.js配置

```javascript
import axios from "axios";
import showMessage from "../utils/showMessage";

// 创建axios实例
const ins = axios.create();

// 响应拦截器
ins.interceptors.response.use(
  function(resp) {
    // 统一处理响应数据
    if (resp.data.code !== 0) {
      // 业务错误处理
      showMessage({
        content: resp.data.msg,
        type: "error",
        duration: 1500,
      });
      return null;
    }
    // 返回业务数据
    return resp.data.data;
  }
);

export default ins;
```

### 拦截器作用

1. **统一错误处理**
   - 检查业务状态码
   - 显示错误提示信息

2. **数据提取**
   - 自动提取响应中的data字段
   - 简化组件中的数据处理

## 本章小结

本章我们完成了文章列表页的基础准备工作：

1. **API接口封装**
   - 创建了blog.js API模块
   - 封装了获取文章列表和分类的接口

2. **递归组件实现**
   - 开发了RightList递归组件
   - 支持多级分类导航展示

3. **数据混入**
   - 创建了fetchData混入
   - 统一处理数据加载状态

4. **Mock数据配置**
   - 配置了文章和分类的Mock数据
   - 支持分页和分类筛选

5. **Axios配置优化**
   - 统一处理响应数据
   - 集中错误处理

## 课后练习

1. 实现Blog页面的完整布局
2. 集成RightList组件显示分类导航
3. 使用fetchData混入获取文章数据
4. 实现文章列表的展示功能
5. 添加加载状态显示

## 下一步

在下一章中，我们将继续完善文章列表页：
- 实现文章列表的具体展示
- 添加分页功能
- 实现分类筛选功能
- 优化用户体验
