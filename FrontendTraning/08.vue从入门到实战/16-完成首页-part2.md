# 16. 完成首页-part2

## 学习目标

- 实现首页轮播图的完整功能
- 理解组件化开发思想
- 掌握Vue中的事件处理和计算属性
- 学习使用CSS动画和过渡效果

## 主要内容

### 1. 首页轮播图组件 Home/index.vue

首页是一个全屏垂直轮播图，支持鼠标滚轮切换、点击箭头切换、点击指示器切换。

#### 核心功能

```vue
<template>
  <div class="home-container" ref="container" @wheel="handleWheel">
    <!-- 轮播图容器 -->
    <ul
      class="carousel-container"
      :style="{ marginTop }"
      @transitionend="handleTransitionEnd"
    >
      <li v-for="(item, i) in banners" :key="item.id">
        <CarouselItem :carousel="item" />
      </li>
    </ul>
    
    <!-- 向上箭头 -->
    <div v-show="index >= 1" @click="switchTo(index - 1)" class="icon icon-up">
      <Icon type="arrowUp" />
    </div>
    
    <!-- 向下箭头 -->
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
        :class="{ active: i === index }"
        v-for="(item, i) in banners"
        :key="item.id"
        @click="switchTo(i)"
      ></li>
    </ul>
  </div>
</template>

<script>
import { getBanners } from "@/api/banner";
import CarouselItem from "./Carouselitem";
import Icon from "@/components/Icon";

export default {
  components: {
    CarouselItem,
    Icon,
  },
  data() {
    return {
      banners: [],           // 轮播图数据数组
      index: 0,              // 当前显示的轮播图索引
      containerHeight: 0,    // 容器高度
      switching: false,      // 是否正在切换中
    };
  },
  async created() {
    // 组件创建时获取轮播图数据
    this.banners = await getBanners();
  },
  mounted() {
    // 组件挂载后获取容器高度
    this.containerHeight = this.$refs.container.clientHeight;
    window.addEventListener("resize", this.handleResize);
  },
  destroyed() {
    // 组件销毁时移除事件监听
    window.removeEventListener("resize", this.handleResize);
  },
  computed: {
    // 计算marginTop实现轮播效果
    marginTop() {
      return -this.index * this.containerHeight + "px";
    },
  },
  methods: {
    // 切换到指定轮播图
    switchTo(i) {
      this.index = i;
    },
    
    // 处理鼠标滚轮事件
    handleWheel(e) {
      if (this.switching) {
        return; // 切换中时忽略滚轮事件
      }
      if (e.deltaY < -5 && this.index > 0) {
        // 向上滚动，显示上一张
        this.switching = true;
        this.index--;
      } else if (e.deltaY > 5 && this.index < this.banners.length - 1) {
        // 向下滚动，显示下一张
        this.switching = true;
        this.index++;
      }
    },
    
    // 过渡动画结束后重置切换状态
    handleTransitionEnd() {
      this.switching = false;
    },
    
    // 窗口大小改变时重新计算容器高度
    handleResize() {
      this.containerHeight = this.$refs.container.clientHeight;
    },
  },
};
</script>

<style lang="less" scoped>
.home-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}

.carousel-container {
  width: 100%;
  height: 100%;
  transition: 500ms; // 过渡动画时长
  li {
    width: 100%;
    height: 100%;
  }
}

// 箭头图标样式
.icon {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-size: 30px;
  color: @gray;
  cursor: pointer;
  
  &.icon-up {
    top: 25px;
    animation: jump-up 2s infinite;
  }
  
  &.icon-down {
    top: auto;
    bottom: 25px;
    animation: jump-down 2s infinite;
  }
}

// 箭头跳动动画
@keyframes jump-up {
  0% { transform: translate(-50%, 5px); }
  50% { transform: translate(-50%, -5px); }
  100% { transform: translate(-50%, 5px); }
}

@keyframes jump-down {
  0% { transform: translate(-50%, -5px); }
  50% { transform: translate(-50%, 5px); }
  100% { transform: translate(-50%, -5px); }
}

// 右侧指示器样式
.indicator {
  position: absolute;
  left: auto;
  right: 20px;
  top: 50%;
  transform: translateY(-50%);
  
  li {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: @words;
    cursor: pointer;
    margin-bottom: 10px;
    border: 1px solid #fff;
    
    &.active {
      background: #fff;
    }
  }
}
</style>
```

#### 关键点说明

1. **计算属性 marginTop**：通过改变marginTop实现轮播图的垂直滚动效果

2. **防抖处理**：使用`switching`状态防止用户快速滚动时的抖动问题

3. **事件监听**：监听窗口resize事件，确保容器高度始终正确

4. **CSS动画**：使用`@keyframes`实现箭头的跳动效果

### 2. 轮播图项组件 CarouselItem.vue

每个轮播图项包含背景图、标题和描述，图片加载完成后文字会有动画效果。

```vue
<template>
  <div class="carousel-item-container">
    <div class="carousel-img">
      <!-- 使用ImageLoader组件实现图片懒加载 -->
      <ImageLoader
        @load="showWords"
        :src="carousel.bigImg"
        :placeholder="carousel.midImg"
      />
    </div>
    <div class="title" ref="title">{{ carousel.title }}</div>
    <div class="desc" ref="desc">{{ carousel.description }}</div>
  </div>
</template>

<script>
import ImageLoader from "@/components/ImageLoader";

export default {
  props: ["carousel"],
  components: {
    ImageLoader,
  },
  data() {
    return {
      titleWidth: 0,
      descWidth: 0,
    };
  },
  mounted() {
    // 获取文字的原始宽度
    this.titleWidth = this.$refs.title.clientWidth;
    this.descWidth = this.$refs.desc.clientWidth;
  },
  methods: {
    // 图片加载完成后显示文字
    showWords() {
      // 标题动画
      this.$refs.title.style.opacity = 1;
      this.$refs.title.style.width = 0;
      this.$refs.title.clientWidth; // 强制浏览器reflow
      this.$refs.title.style.transition = "1s";
      this.$refs.title.style.width = this.titleWidth + "px";

      // 描述动画（延迟1秒开始）
      this.$refs.desc.style.opacity = 1;
      this.$refs.desc.style.width = 0;
      this.$refs.desc.clientWidth; // 强制浏览器reflow
      this.$refs.desc.style.transition = "2s 1s";
      this.$refs.desc.style.width = this.descWidth + "px";
    },
  },
};
</script>

<style lang="less" scoped>
.carousel-item-container {
  width: 100%;
  height: 100%;
  color: #fff;
  position: relative;
}

.carousel-img {
  width: 100%;
  height: 100%;
}

.title,
.desc {
  position: absolute;
  letter-spacing: 3px;
  left: 30px;
  color: #fff;
  text-shadow: 1px 0 0 rgba(0, 0, 0, 0.5), -1px 0 0 rgba(0, 0, 0, 0.5),
    0 1px 0 rgba(0, 0, 0, 0.5), 0 -1px 0 rgba(0, 0, 0, 0.5);
  white-space: nowrap;
  overflow: hidden;
  opacity: 0; // 初始隐藏
}

.title {
  top: calc(50% - 40px);
  font-size: 2em;
}

.desc {
  top: calc(50% + 20px);
  font-size: 1.2em;
}
</style>
```

#### 动画实现原理

1. **文字展开动画**：通过改变width实现文字从左向右展开的效果

2. **强制Reflow**：通过访问`clientWidth`强制浏览器重新渲染，确保过渡动画能正常触发

3. **过渡延迟**：描述文字的动画延迟1秒开始（`transition: "2s 1s"`）

### 3. 图片加载器组件 ImageLoader

实现图片的占位符和原图的平滑过渡效果。

```vue
<template>
  <div class="image-loader-container">
    <!-- 占位图（模糊小图） -->
    <img v-if="!everythingDone" class="placeholder" :src="placeholder" alt="" />
    <!-- 原图 -->
    <img
      @load="handleLoad"
      :src="src"
      alt=""
      :style="{ opacity: originOpacity, transition: `${duration}ms` }"
    />
  </div>
</template>

<script>
export default {
  props: {
    src: {
      type: String,
      required: true, // 原图地址
    },
    placeholder: {
      type: String,
      required: true, // 占位图地址
    },
    duration: {
      type: Number,
      default: 500, // 过渡时长
    },
  },
  data() {
    return {
      originLoaded: false,    // 原图是否加载完成
      everythingDone: false,  // 是否一切都尘埃落定
    };
  },
  computed: {
    originOpacity() {
      return this.originLoaded ? 1 : 0; // 控制原图透明度
    },
  },
  methods: {
    handleLoad() {
      this.originLoaded = true;
      // 等待过渡动画完成后，移除占位图
      setTimeout(() => {
        this.everythingDone = true;
        this.$emit("load"); // 通知父组件图片加载完成
      }, this.duration);
    },
  },
};
</script>

<style scoped lang="less">
.image-loader-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}

img {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.placeholder {
  filter: blur(2vw); // 占位图模糊效果
}
</style>
```

#### 图片加载流程

1. 初始状态：显示模糊的占位图，原图透明度为0

2. 原图加载完成：`handleLoad`触发，原图透明度渐变到1

3. 过渡完成：移除占位图，触发父组件的`load`事件

### 4. API调用

```javascript
// api/banner.js
import request from "./request";

export async function getBanners() {
  return await request.get("/api/banner");
}
```

## 知识点总结

### 1. Vue组件通信

- **Props Down**：父组件通过props向子组件传递数据
- **Events Up**：子组件通过`$emit`向父组件发送事件

### 2. 计算属性

- 基于响应式依赖进行缓存
- 只有依赖变化时才重新计算
- 适合用于复杂的派生数据

### 3. 生命周期钩子

- `created`：组件实例创建完成，可访问data、computed等
- `mounted`：DOM挂载完成，可进行DOM操作
- `destroyed`：组件销毁，可进行清理工作

### 4. 事件处理

- 使用`@`或`v-on`绑定事件
- 修饰符：`.stop`、`.prevent`、`.once`等
- 自定义事件：`$emit`触发，`@`监听

### 5. CSS动画

- **Transition**：用于简单的状态变化
- **Animation**：用于复杂的关键帧动画
- **Transform**：实现位移、缩放、旋转等效果

### 6. 性能优化

- **图片懒加载**：先加载小图，再加载原图
- **事件防抖**：防止用户快速操作导致的问题
- **计算属性缓存**：避免重复计算

## 思考题

1. 为什么要强制浏览器reflow？如何实现？
2. 如何实现轮播图的自动播放功能？
3. 如何优化大量图片的加载性能？
4. 计算属性和方法的区别是什么？

## 下节预告

下一节我们将学习如何完成博客文章列表页面，实现文章数据的展示和分页功能。
