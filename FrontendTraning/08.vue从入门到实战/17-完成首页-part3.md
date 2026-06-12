# 17. 完成首页-part3

## 章节概述

本章节完成首页轮播图的核心功能实现，包括：

1. 轮播图的切换控制（鼠标滚轮、点击箭头、点击指示器）
2. 轮播图项的视觉交互效果（图片视差、文字动画）
3. 响应式处理

## 核心知识点

### 1. 轮播图容器组件 (index.vue)

#### 关键状态管理

```javascript
data() {
  return {
    banners: [],          // 轮播图数据数组
    index: 0,             // 当前显示的轮播图索引
    containerHeight: 0,   // 容器高度
    switching: false,     // 是否正在切换中（防止快速滚动）
  };
}
```

#### 计算属性：marginTop

通过计算属性实现轮播图的垂直滚动效果：

```javascript
computed: {
  marginTop() {
    // 通过改变marginTop实现切换，每个轮播图占满容器高度
    return -this.index * this.containerHeight + "px";
  },
}
```

#### 鼠标滚轮事件处理

```javascript
handleWheel(e) {
  // 如果正在切换中，忽略滚动事件
  if (this.switching) {
    return;
  }
  
  // deltaY < 0 表示向上滚动，deltaY > 0 表示向下滚动
  // 设置阈值避免误触
  if (e.deltaY < -5 && this.index > 0) {
    // 往上滚动（显示上一张）
    this.switching = true;
    this.index--;
  } else if (e.deltaY > 5 && this.index < this.banners.length - 1) {
    // 往下滚动（显示下一张）
    this.switching = true;
    this.index++;
  }
}
```

#### 过渡动画结束处理

```javascript
handleTransitionEnd() {
  // CSS过渡动画完成后，重置switching状态，允许下一次切换
  this.switching = false;
}
```

#### 箭头导航按钮

```vue
<!-- 向上箭头：当前索引大于0时显示 -->
<div v-show="index >= 1" @click="switchTo(index - 1)" class="icon icon-up">
  <Icon type="arrowUp" />
</div>

<!-- 向下箭头：未到最后一张时显示 -->
<div v-show="index < banners.length - 1" @click="switchTo(index + 1)" 
     class="icon icon-down">
  <Icon type="arrowDown" />
</div>
```

#### 指示器导航

```vue
<ul class="indicator">
  <li
    :class="{ active: i === index }"
    v-for="(item, i) in banners"
    :key="item.id"
    @click="switchTo(i)"
  ></li>
</ul>
```

#### 跳转方法

```javascript
methods: {
  // 切换到指定索引的轮播图
  switchTo(i) {
    this.index = i;
  },
}
```

### 2. 轮播图项组件 (CarouselItem.vue)

#### 视差效果实现

核心思想：根据鼠标在容器中的位置，动态计算图片的偏移量。

```javascript
computed: {
  // 计算图片的transform属性值
  imagePosition() {
    if (!this.innerSize || !this.containerSize) {
      return;
    }
    
    // 计算超出的宽度和高度
    const extraWidth = this.innerSize.width - this.containerSize.width;
    const extraHeight = this.innerSize.height - this.containerSize.height;
    
    // 根据鼠标位置比例计算偏移量
    const left = (-extraWidth / this.containerSize.width) * this.mouseX;
    const top = (-extraHeight / this.containerSize.height) * this.mouseY;
    
    return {
      transform: `translate(${left}px, ${top}px)`,
    };
  },
}
```

#### 鼠标位置追踪

```javascript
methods: {
  // 鼠标移动时更新坐标
  handleMouseMove(e) {
    const rect = this.$refs.container.getBoundingClientRect();
    this.mouseX = e.clientX - rect.left;
    this.mouseY = e.clientY - rect.top;
  },
  
  // 鼠标离开时重置到中心位置
  handleMouseLeave() {
    this.mouseX = this.center.x;
    this.mouseY = this.center.y;
  },
}
```

#### 文字动画效果

利用CSS transition和浏览器reflow机制实现文字从0宽度到实际宽度的动画：

```javascript
methods: {
  // 图片加载完成后调用，显示文字动画
  showWords() {
    const title = this.$refs.title;
    const desc = this.$refs.desc;
    
    // 标题动画（1秒）
    title.style.opacity = 1;
    title.style.width = 0;
    title.clientWidth; // 强制浏览器reflow
    title.style.transition = "1s";
    title.style.width = this.titleWidth + "px";
    
    // 描述动画（2秒，延迟1秒开始）
    desc.style.opacity = 1;
    desc.style.width = 0;
    desc.clientWidth; // 强制浏览器reflow
    desc.style.transition = "2s 1s";
    desc.style.width = this.descWidth + "px";
  },
}
```

#### 响应式尺寸处理

```javascript
methods: {
  // 更新容器和图片的尺寸信息
  setSize() {
    this.containerSize = {
      width: this.$refs.container.clientWidth,
      height: this.$refs.container.clientHeight,
    };
    this.innerSize = {
      width: this.$refs.image.clientWidth,
      height: this.$refs.image.clientHeight,
    };
  },
},

mounted() {
  // ...其他代码
  this.setSize();
  window.addEventListener("resize", this.setSize);
},

destroyed() {
  window.removeEventListener("resize", this.setSize);
}
```

### 3. 样式要点

#### 箭头动画

```less
.icon {
  .self-center(); // 居中定位的mixin
  font-size: 30px;
  color: @gray;
  cursor: pointer;
  transform: translateX(-50%);
  
  &.icon-up {
    top: 25px;
    animation: jump-up 2s infinite; // 跳跃动画
  }
  
  &.icon-down {
    top: auto;
    bottom: 25px;
    animation: jump-down 2s infinite;
  }
}

@keyframes jump-up {
  0% { transform: translate(-50%, 5px); }
  50% { transform: translate(-50%, -5px); }
  100% { transform: translate(-50%, 5px); }
}
```

#### 指示器样式

```less
.indicator {
  .self-center();
  right: 20px;
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
      background: #fff; // 激活状态
    }
  }
}
```

#### 轮播图项样式

```less
.carousel-item-container {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}

.carousel-img {
  width: 110%;  // 比容器大，实现视差效果
  height: 110%;
  position: absolute;
  transition: 0.3s; // 平滑移动
}

.title, .desc {
  position: absolute;
  white-space: nowrap;
  overflow: hidden;
  opacity: 0; // 初始不可见
  
  // 文字阴影效果
  text-shadow: 1px 0 0 rgba(0, 0, 0, 0.5), 
               -1px 0 0 rgba(0, 0, 0, 0.5),
               0 1px 0 rgba(0, 0, 0, 0.5), 
               0 -1px 0 rgba(0, 0, 0, 0.5);
}
```

## 技术要点总结

### 1. 事件处理

- **滚轮事件**：通过`wheel`事件监听，设置阈值防止误触
- **点击事件**：箭头和指示器的点击切换
- **鼠标事件**：mousemove追踪位置，mouseleave重置状态

### 2. 动画技巧

- **CSS Transition**：利用transition实现平滑过渡
- **动画队列**：标题和描述动画依次执行（使用delay）
- **浏览器Reflow**：通过读取clientWidth强制浏览器渲染

### 3. 状态管理

- **防抖处理**：使用switching状态防止快速切换
- **边界检查**：确保索引不会超出范围
- **响应式更新**：监听resize事件更新尺寸

### 4. 性能优化

- **事件节流**：通过transitionend控制切换频率
- **条件渲染**：使用v-show而非v-if减少DOM操作
- **计算属性缓存**：避免重复计算

## 课后练习

1. 尝试添加键盘左右方向键切换轮播图
2. 实现轮播图自动播放功能（可选配置项）
3. 添加切换进度条或倒计时显示
4. 优化移动端触摸滑动体验

## 下节预告

下一章节我们将完成首页的其他模块，包括文章列表、数据统计等内容。