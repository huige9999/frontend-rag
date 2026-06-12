# 29. 开发文章详情页-part5

## 本节重点

- 文章详情页的布局结构
- 目录组件的交互实现
- 评论组件的加载与提交
- 滚动事件的优化处理

## 页面整体结构

文章详情页采用左右布局结构：
- 左侧：文章详情 + 评论列表
- 右侧：文章目录（TOC）

### Detail.vue - 主页面组件

```vue
<template>
  <Layout>
    <!-- 左侧主内容区域 -->
    <div ref="mainContainer" class="main-container" v-loading="isLoading">
      <BlogDetail :blog="data" v-if="data" />
      <BlogComment v-if="!isLoading" />
    </div>
    <!-- 右侧目录区域 -->
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
  mixins: [fetchData(null)],
  methods: {
    // 获取文章详情数据
    async fetchData() {
      return await getBlog(this.$route.params.id);
    },
    // 处理主容器滚动事件
    handleScroll() {
      this.$bus.$emit("mainScroll", this.$refs.mainContainer);
    },
    // 设置主容器滚动位置
    handleSetMainScroll(scrollTop) {
      this.$refs.mainContainer.scrollTop = scrollTop;
    },
  },
  mounted() {
    // 监听来自目录组件的滚动设置请求
    this.$bus.$on("setMainScroll", this.handleSetMainScroll);
    this.$refs.mainContainer.addEventListener("scroll", this.handleScroll);
  },
  beforeDestroy() {
    // 清理事件监听
    this.$bus.$emit("mainScroll");
    this.$refs.mainContainer.removeEventListener("scroll", this.handleScroll);
    this.$bus.$off("setMainScroll", this.handleSetMainScroll);
  },
  updated() {
    // 处理锚点定位，确保刷新后滚动到正确位置
    const hash = location.hash;
    location.hash = "";
    setTimeout(() => {
      location.hash = hash;
    }, 50);
  },
};
</script>
```

**核心功能说明：**
1. 使用 `fetchData` mixin 统一处理数据加载和加载状态
2. 通过事件总线协调各组件间的滚动通信
3. 锚点定位处理确保刷新页面后滚动位置正确

## 文章详情组件

### BlogDetail.vue

```vue
<template>
  <div class="blog-detail-container">
    <h1>{{ blog.title }}</h1>
    <div class="aside">
      <span>日期: {{ formatDate(blog.createDate) }}</span>
      <span>浏览: {{ blog.scanNumber }}</span>
      <a href="#data-form-container">评论: {{ blog.commentNumber }}</a>
      <RouterLink
        :to="{
          name: 'CategoryBlog',
          params: {
            categoryId: blog.category.id,
          },
        }"
      >
        {{ blog.category.name }}
      </RouterLink>
    </div>
    <!-- 使用v-html渲染markdown转换后的HTML内容 -->
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
```

**功能要点：**
- 显示文章标题、日期、浏览量、评论数等信息
- 使用 `v-html` 指令渲染富文本内容
- 分类链接可跳转到对应分类的文章列表
- 引入 highlight.js 样式实现代码高亮

## 目录组件（TOC）

### BlogTOC.vue

目录组件实现以下功能：
1. 展示文章目录结构
2. 根据滚动位置自动高亮当前章节
3. 点击目录项滚动到对应位置

```vue
<template>
  <div class="blog-toc-container">
    <h2>目录</h2>
    <RightList :list="tocWithSelect" @select="handleSelect" />
  </div>
</template>

<script>
import RightList from "./RightList";
import { debounce } from "@/utils";

export default {
  components: {
    RightList,
  },
  props: {
    toc: {
      type: Array,
    },
  },
  data() {
    return {
      activeAnchor: "", // 当前激活的锚点
    };
  },
  computed: {
    // 根据toc属性以及activeAnchor得到带有isSelect属性的toc数组
    tocWithSelect() {
      const getTOC = (toc = []) => {
        return toc.map((t) => ({
          ...t,
          isSelect: t.anchor === this.activeAnchor,
          children: getTOC(t.children),
        }));
      };
      return getTOC(this.toc);
    },
    // 根据toc得到它们对应的元素数组
    doms() {
      const doms = [];
      const addToDoms = (toc) => {
        for (const t of toc) {
          doms.push(document.getElementById(t.anchor));
          if (t.children && t.children.length) {
            addToDoms(t.children);
          }
        }
      };
      addToDoms(this.toc);
      return doms;
    },
  },
  created() {
    // 使用防抖优化滚动事件处理
    this.setSelectDebounce = debounce(this.setSelect, 50);
    this.$bus.$on("mainScroll", this.setSelectDebounce);
  },
  destroyed() {
    this.$bus.$off("mainScroll", this.setSelectDebounce);
  },
  methods: {
    // 点击目录项，滚动到对应位置
    handleSelect(item) {
      location.hash = item.anchor;
    },
    // 设置activeAnchor为正确的值
    setSelect(scrollDom) {
      if (!scrollDom) {
        return;
      }
      this.activeAnchor = ""; // 先清空之前的状态
      const range = 200; // 定义激活范围
      
      for (const dom of this.doms) {
        if (!dom) continue;
        
        // 得到元素离视口顶部的距离
        const top = dom.getBoundingClientRect().top;
        
        if (top >= 0 && top <= range) {
          // 在规定的范围内
          this.activeAnchor = dom.id;
          return;
        } else if (top > range) {
          // 在规定的范围下方
          return;
        } else {
          // 在规定的范围上方，先假设自己是激活的
          this.activeAnchor = dom.id;
        }
      }
    },
  },
};
</script>
```

**核心实现逻辑：**

1. **计算属性 tocWithSelect**：为每个目录项添加 `isSelect` 属性，用于高亮显示

2. **计算属性 doms**：将目录锚点对应的 DOM 元素收集到数组中

3. **setSelect 方法**：根据滚动位置判断哪个目录项应该被激活
   - 遍历所有目录对应的 DOM 元素
   - 通过 `getBoundingClientRect().top` 获取元素距离视口顶部的距离
   - 在规定范围内（200px）的元素被标记为激活状态

4. **防抖优化**：使用 `debounce` 减少滚动事件的处理频率，提升性能

## 评论组件

### BlogComment.vue

评论组件实现以下功能：
1. 展示评论列表
2. 支持提交新评论
3. 滚动到底部自动加载更多评论

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
  mixins: [fetchData({ total: 0, rows: [] })],
  components: {
    MessageArea,
  },
  data() {
    return {
      page: 1,
      limit: 10,
    };
  },
  created() {
    this.$bus.$on("mainScroll", this.handleScroll);
  },
  destroyed() {
    this.$bus.$off("mainScroll", this.handleScroll);
  },
  computed: {
    // 判断是否还有更多评论
    hasMore() {
      return this.data.rows.length < this.data.total;
    },
  },
  methods: {
    // 处理滚动事件，实现加载更多
    handleScroll(dom) {
      if (this.isLoading || !dom) {
        return;
      }
      const range = 100; // 可接受的范围
      const dec = Math.abs(dom.scrollTop + dom.clientHeight - dom.scrollHeight);
      
      if (dec <= range) {
        this.fetchMore();
      }
    },
    // 获取评论数据
    async fetchData() {
      return await getComments(this.$route.params.id, this.page, this.limit);
    },
    // 加载下一页评论
    async fetchMore() {
      if (!this.hasMore) {
        return;
      }
      this.isLoading = true;
      this.page++;
      const resp = await this.fetchData();
      this.data.total = resp.total;
      this.data.rows = this.data.rows.concat(resp.rows);
      this.isLoading = false;
    },
    // 提交新评论
    async handleSubmit(formData, callback) {
      const resp = await postComment({
        blogId: this.$route.params.id,
        ...formData,
      });
      this.data.rows.unshift(resp);
      this.data.total++;
      callback("评论成功");
    },
  },
};
</script>
```

**核心功能实现：**

1. **分页加载**：
   - 使用 `page` 和 `limit` 控制分页
   - `hasMore` 计算属性判断是否还有更多数据

2. **滚动加载更多**：
   - 监听主容器滚动事件
   - 计算当前滚动位置是否接近底部
   - 触发加载下一页数据

3. **提交评论**：
   - 调用 API 提交评论数据
   - 将新评论插入到列表开头
   - 更新评论总数
   - 通过回调通知子组件处理完成

## 事件总线通信

本页面使用事件总线（Event Bus）实现组件间通信：

```javascript
// 在 main.js 中
Vue.prototype.$bus = new Vue();

// 发送事件
this.$bus.$emit("mainScroll", this.$refs.mainContainer);

// 监听事件
this.$bus.$on("mainScroll", this.setSelectDebounce);

// 移除监听
this.$bus.$off("mainScroll", this.setSelectDebounce);
```

**通信场景：**
1. Detail.vue → BlogTOC.vue：传递主容器滚动事件
2. BlogTOC.vue → Detail.vue：请求设置滚动位置
3. Detail.vue → BlogComment.vue：传递滚动事件用于加载更多

## 性能优化要点

1. **防抖处理**：滚动事件使用防抖函数减少触发频率
2. **条件渲染**：使用 `v-if` 和 `v-loading` 优化加载体验
3. **事件清理**：组件销毁时移除事件监听，避免内存泄漏
4. **锚点定位优化**：在 `updated` 钩子中处理锚点定位，确保刷新后位置正确

## 总结

本节完成了文章详情页的最后部分，主要包括：
- 文章详情组件的展示
- 目录组件的交互实现
- 评论组件的分页加载
- 组件间的事件通信
- 性能优化处理

通过这些组件的组合，实现了一个功能完整的文章详情页，提供了良好的用户体验。
