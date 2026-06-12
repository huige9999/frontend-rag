# 28. 开发文章详情页-part4

## 本节目标

本节继续完善文章详情页，主要实现以下功能：

1. **评论功能**：集成评论区组件，实现评论的展示和提交
2. **目录高亮**：实现滚动时目录的自动高亮
3. **滚动体验优化**：处理页面滚动和锚点跳转的交互

## 核心组件

### 1. Detail.vue - 主容器组件

```vue
<template>
  <Layout>
    <div ref="mainContainer" class="main-container" v-loading="isLoading">
      <BlogDetail :blog="data" v-if="data" />
      <BlogComment v-if="!isLoading" />
    </div>
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
    // 处理滚动事件，通知其他组件
    handleScroll() {
      this.$bus.$emit("mainScroll", this.$refs.mainContainer);
    },
  },
  mounted() {
    // 监听主容器滚动事件
    this.$refs.mainContainer.addEventListener("scroll", this.handleScroll);
  },
  destroyed() {
    // 组件销毁时移除事件监听
    this.$refs.mainContainer.removeEventListener("scroll", this.handleScroll);
  },
  updated() {
    // 处理锚点跳转，确保跳转到正确位置
    const hash = location.hash;
    location.hash = "";
    setTimeout(() => {
      location.hash = hash;
    }, 50);
  },
};
</script>

<style scoped lang="less>
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

**关键点说明：**

- **v-loading**：使用loading指令显示加载状态
- **事件总线**：通过$bus传递滚动事件，实现组件间通信
- **锚点处理**：在updated钩子中重新设置hash，确保路由跳转后正确滚动到锚点位置
- **生命周期管理**：在destroyed中移除事件监听，防止内存泄漏

### 2. BlogComment.vue - 评论组件

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
  methods: {
    // 获取评论列表
    async fetchData() {
      return await getComments(this.$route.params.id, this.page, this.limit);
    },
    // 提交评论
    async handleSubmit(formData, callback) {
      const resp = await postComment({
        blogId: this.$route.params.id,
        ...formData,
      });
      // 将新评论添加到列表开头
      this.data.rows.unshift(resp);
      this.data.total++;
      callback("评论成功"); // 通知子组件处理完成
    },
  },
};
</script>

<style scoped lang="less>
.blog-comment-container {
  margin: 30px 0;
}
</style>
```

**关键点说明：**

- **复用MessageArea组件**：评论组件复用了留言区域的组件
- **分页数据**：使用page和limit控制分页
- **评论提交**：提交成功后将新评论插入数组开头，并更新总数
- **回调机制**：通过callback通知子组件处理完成，实现组件间协作

### 3. BlogTOC.vue - 目录组件

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
    // 根据toc和activeAnchor计算带有选中状态的目录
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
    // 获取目录对应的DOM元素数组
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
    // 处理目录项点击
    handleSelect(item) {
      location.hash = item.anchor;
    },
    // 设置当前激活的目录项
    setSelect() {
      this.activeAnchor = "";
      const range = 200; // 顶部有效范围
      
      for (const dom of this.doms) {
        if (!dom) continue;
        
        // 获取元素距离视口顶部的距离
        const top = dom.getBoundingClientRect().top;
        
        if (top >= 0 && top <= range) {
          // 在有效范围内，选中当前项
          this.activeAnchor = dom.id;
          return;
        } else if (top > range) {
          // 在范围下方，返回
          return;
        } else {
          // 在范围上方，继续查找
          this.activeAnchor = dom.id;
        }
      }
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

**关键点说明：**

- **计算属性tocWithSelect**：递归处理目录结构，为每个节点添加选中状态
- **计算属性doms**：递归获取所有目录项对应的DOM元素
- **防抖优化**：使用debounce减少滚动事件的触发频率
- **滚动位置计算**：通过getBoundingClientRect()判断元素位置，设置合适的激活项

### 4. RightList.vue - 通用右侧列表组件

```vue
<template>
  <ul class="right-list-container">
    <li v-for="(item, i) in list" :key="i">
      <span @click="handleClick(item)" :class="{ active: item.isSelect }">
        {{ item.name }}
      </span>
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
      // 只有未选中的项才能被点击
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

**关键点说明：**

- **递归组件**：组件可以自己调用自己，处理嵌套的树形结构
- **样式递归**：通过嵌套的样式实现子列表的缩进
- **点击事件**：只有非选中项才能触发点击，避免重复操作
- **高亮显示**：使用active类和主题色显示选中状态

## 技术要点

### 1. 组件间通信

- **事件总线**：通过`this.$bus.$on`和`this.$bus.$emit`实现跨组件通信
- **父子通信**：通过props和$emit实现父子组件通信
- **滚动事件共享**：主容器的滚动事件传递给目录组件，实现联动

### 2. 性能优化

- **防抖处理**：使用debounce减少滚动事件的触发频率
- **计算属性缓存**：tocWithSelect和doms使用计算属性，自动缓存结果
- **条件渲染**：使用v-if减少不必要的组件渲染

### 3. 用户体验优化

- **平滑滚动**：使用CSS的scroll-behavior: smooth实现平滑滚动
- **加载状态**：使用v-loading指令显示加载状态
- **锚点处理**：在updated钩子中处理锚点跳转，确保定位准确
- **高亮反馈**：目录根据滚动位置自动高亮，提供位置反馈

### 4. 代码组织

- **组件复用**：MessageArea组件同时用于留言和评论
- **mixin复用**：fetchData mixin统一处理数据加载
- **递归组件**：RightList组件处理任意层级的树形结构
- **样式模块化**：使用scoped和变量管理样式

## 实现思路

1. **评论功能集成**
   - 复用现有的MessageArea组件
   - 通过API获取评论列表
   - 处理评论提交并更新本地数据

2. **目录高亮实现**
   - 监听主容器的滚动事件
   - 根据滚动位置计算应该高亮的目录项
   - 使用防抖优化性能

3. **滚动体验优化**
   - 实现平滑滚动效果
   - 处理锚点跳转的边界情况
   - 确保滚动和目录高亮同步

4. **组件协调**
   - 使用事件总线传递滚动事件
   - 通过props和events实现组件间协作
   - 合理管理生命周期，避免内存泄漏

## 总结

本节完成了文章详情页的核心功能，包括：

1. ✅ 集成评论功能，实现评论的展示和提交
2. ✅ 实现目录滚动高亮，提升阅读体验
3. ✅ 优化滚动和锚点跳转的交互体验
4. ✅ 通过递归组件处理复杂的树形结构
5. ✅ 使用事件总线和计算属性优化组件通信

这些功能共同构成了一个完整的文章详情页，为用户提供了良好的阅读和交互体验。
