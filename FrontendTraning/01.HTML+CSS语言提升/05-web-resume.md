# 05. 网页简历

## 本节目标

学习如何使用HTML和CSS创建一个专业的个人简历网页，掌握页面布局、样式设计和视觉效果的综合运用。

## 核心知识点

### 1. 页面整体布局设计
- 固定宽度容器设计
- 居中布局实现
- 阴影和圆角效果
- 伪元素装饰效果

### 2. Grid网格布局
- 个人信息区域的网格布局
- 多列不等宽网格
- 项目经验的结构化布局

### 3. 时间线效果
- 伪元素创建时间轴
- 圆点标记的实现
- 相对定位的运用

### 4. 技能展示可视化
- 圆形进度条设计
- 边框颜色控制
- 旋转变换效果

### 5. 响应式设计基础
- 移动端适配考虑
- 弹性布局运用

## HTML结构设计

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>邓旭明的个人简历</title>
    <!-- 引入图标字体 -->
    <link rel="stylesheet" href="http://at.alicdn.com/t/font_2514356_cbh3ncg5i96.css" />
    <link rel="stylesheet" href="./index.css" />
  </head>
  <body>
    <!-- 主容器：控制页面整体布局 -->
    <div class="container">
      
      <!-- 个人信息区域：使用flex布局 -->
      <div class="info-container">
        <div class="info">
          <!-- 姓名和职位 -->
          <div class="name">
            邓旭明
            <span>前端工程师</span>
          </div>
          <!-- 联系方式：电话 -->
          <i class="iconfont label icondianhua"></i>
          <a href="tel:13801010101">138 0101 0101</a>
          
          <!-- 学历信息 -->
          <label class="label">学历</label>
          <span>研究生</span>
          
          <!-- 邮箱 -->
          <i class="iconfont iconyoujian label"></i>
          <a href="mailto:dengxuming@duyi-inc.com">dengxuming@duyi-inc.com</a>
          
          <!-- 生日 -->
          <label class="label">生日</label>
          <span>2010-01-01</span>
          
          <!-- 个人网站 -->
          <i class="iconfont label iconwangzhan"></i>
          <a href="http://www.dengxuming.com" target="_blank">
            www.dengxuming.com
          </a>
          
          <!-- 开发经验 -->
          <label class="label">开发经验</label>
          <span>5年</span>
        </div>
        <!-- 头像图片 -->
        <img src="./img/avatar.png" alt="个人头像" />
      </div>

      <!-- 教育经历模块 -->
      <div class="block">
        <h2 class="block-title">教育经历</h2>
        <div class="block-content time-line">
          <div class="item">
            <span class="gray">2005 ~ 2009</span>
            <span>克莱登大学</span>
            <span class="gray">计算机科学与技术</span>
          </div>
        </div>
      </div>

      <!-- 工作经历模块 -->
      <div class="block">
        <h2 class="block-title">工作经历</h2>
        <div class="block-content time-line">
          <div class="item">
            <span class="gray">2019 ~ 2024</span>
            <span>某互联网公司</span>
            <span class="gray">前端开发工程师</span>
            <div class="detail gray">
              负责公司核心产品的前端开发工作，使用Vue.js框架构建用户界面，
              参与组件库的开发和维护，优化页面性能，提升用户体验。
            </div>
          </div>
        </div>
      </div>

      <!-- 专业技能模块 -->
      <div class="block">
        <h2 class="block-title">专业技能</h2>
        <div class="block-content skills">
          <div class="item level1">HTML/CSS</div>
          <div class="item level2">JavaScript</div>
          <div class="item level3">Vue.js</div>
          <div class="item level4">React</div>
          <div class="item level2">Node.js</div>
          <div class="item level4">TypeScript</div>
        </div>
      </div>

      <!-- 项目经验模块 -->
      <div class="block">
        <h2 class="block-title">项目经验</h2>
        <div class="block-content">
          <div class="projects">
            <h3>在线时间管理系统</h3>
            <span class="label gray">访问地址</span>
            <a href="https://www.todolist.com" target="_blank">www.todolist.com</a>
            <span class="label gray">github</span>
            <a href="https://github.com" target="_blank">www.github.com</a>
            <span class="label gray">项目背景</span>
            <span>
              本系统在学习vue后，凭个人兴趣，开发的用于进行时间管理的系统
            </span>
            <span class="label gray">项目实现</span>
            <div>
              <p>
                系统包含<strong>任务清单</strong>、<strong>任务分类</strong>、
                <strong>日志薄</strong>、<strong>废纸篓</strong>等模块，
                本人参与了所有模块的开发。
              </p>
              <p>
                系统分为前后端两个部分。前端部分使用<strong>vue</strong>作为框架，
                并使用脚手架<strong>vue-cli</strong>搭建。
              </p>
              <p>
                其中，使用<strong>vue-router</strong>处理路由，
                使用<strong>vuex</strong>处理共享数据，
                使用<strong>element-ui</strong>构建界面。
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- 个人评价模块 -->
      <div class="block">
        <h2 class="block-title">个人评价</h2>
        <div class="block-content intro">
          热爱前端开发，具备良好的学习能力和团队协作精神。
          关注前端技术发展，乐于尝试新技术，追求代码质量和用户体验的完美结合。
        </div>
      </div>
    </div>
  </body>
</html>
```

## CSS样式详解

### 全局样式重置

```css
/* 全局样式重置：清除默认边距 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* HTML根元素样式设置 */
html {
  font-size: 14px;        /* 基础字体大小 */
  line-height: 1.5;       /* 行高，提升可读性 */
  letter-spacing: 1px;    /* 字间距，增加视觉舒适度 */
  color: #333;            /* 文字颜色 */
}

/* 链接样式 */
a {
  color: #0ea5e9;                    /* 链接颜色：天蓝色 */
  text-decoration: none;             /* 去除下划线 */
}
```

### 容器布局与装饰效果

```css
/* 主容器样式 */
.container {
  width: 800px;                      /* 固定宽度 */
  padding: 50px;                     /* 内边距 */
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);  /* 阴影效果 */
  margin: 50px auto;                /* 上下50px，左右居中 */
  border-radius: 20px;              /* 圆角 */
  position: relative;               /* 相对定位，为伪元素定位参考 */
  background: #fff;                 /* 白色背景 */
}

/* 装饰性背景效果：使用伪元素创建倾斜背景 */
.container::before {
  content: '';                      /* 必须设置content属性 */
  position: absolute;
  width: 100%;
  height: 100%;
  background: linear-gradient(to right, #22d3ee, #0ea5e9);  /* 渐变背景 */
  left: 0;
  top: 0;
  border-radius: 20px;
  z-index: -1;                      /* 放在内容下方 */
  transform: rotate(-3deg);         /* 逆时针旋转3度 */
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
}
```

**设计要点：**
- `linear-gradient` 创建从青色到天蓝色的渐变
- `z-index: -1` 确保装饰背景在内容下方
- `transform: rotate(-3deg)` 创建倾斜效果，增加视觉动感

### 个人信息区域 - Grid布局

```css
/* 信息容器：使用Flex布局 */
.info-container {
  display: flex;                     /* 弹性布局 */
}

/* 头像样式 */
.info-container img {
  width: 150px;
  height: 150px;
  object-fit: cover;                /* 保持图片比例，裁剪多余部分 */
  border-radius: 50%;                /* 圆形头像 */
}

/* 信息区域：使用Grid网格布局 */
.info {
  flex-grow: 1;                      /* 占据剩余空间 */
  margin-right: 20px;
  display: grid;                     /* 网格布局 */
  grid-template-columns: 20px 280px 60px 1fr;  /* 四列：图标、内容、标签、详情 */
  column-gap: 10px;                  /* 列间距 */
}

/* 姓名样式：跨越所有列 */
.info .name {
  grid-area: 1/1/2/5;               /* 从第1行第1列到第2行第5列 */
  font-size: 2em;                    /* 放大字体 */
  letter-spacing: 3px;
  font-weight: bold;
}

/* 职位描述 */
.info .name span {
  font-size: 14px;                  /* 较小字体 */
}

/* 标签样式 */
.label {
  color: #888;                       /* 灰色文字 */
  text-align: right;                 /* 右对齐 */
}
```

**Grid布局要点：**
- `grid-template-columns` 定义4列不等宽布局
- `grid-area` 让姓名跨越所有列
- 图标、内容、标签、详情按列对齐

### 时间线效果实现

```css
/* 时间轴容器 */
.time-line {
  position: relative;                /* 为伪元素定位 */
}

/* 时间轴线：左侧垂直线 */
.time-line::before {
  content: '';
  position: absolute;
  width: 2px;                        /* 线条宽度 */
  height: 100%;
  background: #22d3ee;               /* 青色线条 */
  left: 0;
  top: 10px;
}

/* 时间轴项目 */
.time-line .item {
  padding-left: 20px;                /* 为时间轴留出空间 */
  margin: 20px 0;
  position: relative;
}

/* 时间轴圆点 */
.time-line .item::before {
  content: '';
  position: absolute;
  width: 15px;                       /* 圆点大小 */
  height: 15px;
  border-radius: 50%;                /* 圆形 */
  background: #0ea5e9;               /* 天蓝色 */
  left: -7px;                        /* 定位到线条上 */
  top: 3px;
}
```

**时间线设计要点：**
- 使用伪元素创建垂直线条和圆点
- `position: relative` 和 `absolute` 配合实现精确定位
- 圆点通过 `left: -7px` (15px/2 - 2px/2) 居中于线条

### 技能展示 - 圆形进度条

```css
/* 技能网格布局 */
.skills {
  display: grid;
  grid-template-columns: repeat(3, 1fr);  /* 3列等宽 */
  justify-items: center;                   /* 水平居中 */
  row-gap: 30px;                           /* 行间距 */
}

/* 技能项样式 */
.skills .item {
  background: #f0fdff;                /* 浅蓝色背景 */
  width: 130px;
  height: 130px;
  border-radius: 50%;                /* 圆形 */
  text-align: center;
  line-height: 130px;                /* 垂直居中文字 */
  position: relative;
}

/* 进度条边框：使用伪元素 */
.skills .item::before {
  content: '';
  position: absolute;
  border: 5px solid #c3f1f8;        /* 默认浅蓝色边框 */
  transform: rotate(45deg);          /* 旋转45度 */
  width: 100%;
  height: 100%;
  border-radius: 50%;
  left: 0;
  top: 0;
  box-sizing: border-box;
}

/* 不同熟练度级别 */
.skills .item.level1::before {
  border-top-color: #0ea5e9;        /* 25%：上边框 */
}

.skills .item.level2::before {
  border-top-color: #0ea5e9;        /* 50%：上+右 */
  border-right-color: #0ea5e9;
}

.skills .item.level3::before {
  border-top-color: #0ea5e9;        /* 75%：上+右+下 */
  border-right-color: #0ea5e9;
  border-bottom-color: #0ea5e9;
}

.skills .item.level4::before {
  border-color: #0ea5e9;            /* 100%：全部边框 */
}
```

**圆形进度条设计要点：**
- 通过旋转45度的边框模拟圆形进度条
- 不同级别显示不同数量的有色边框
- `border-top-color` 等属性单独控制边框颜色

### 项目经验布局

```css
/* 项目容器：Grid布局 */
.projects {
  margin-bottom: 30px;
  display: grid;
  grid-template-columns: 60px 1fr;   /* 标签列 + 内容列 */
  gap: 10px;                          /* 间距 */
}

/* 标题跨越所有列 */
.projects h3 {
  grid-area: 1/1/1/3;
}

/* 段落样式 */
.projects p {
  margin-bottom: 1em;
}
```

### 其他辅助样式

```css
/* 通用模块样式 */
.block {
  margin: 30px 0;                     /* 模块间距 */
}

.block-title {
  border-bottom: 1px solid #ccc;      /* 底部边框 */
  padding: 10px 0;
}

.block-content {
  padding: 20px 0;                    /* 内容区域内边距 */
}

/* 灰色文字 */
.gray {
  color: #888;
}

/* 详情描述 */
.detail {
  font-size: 12px;                    /* 较小字体 */
  margin-top: 10px;
}

/* 个人评价 */
.intro {
  font-size: 16px;
  line-height: 2;                     /* 增加行高提升可读性 */
}
```

## 关键技术要点

### 1. 语义化HTML结构
- 使用 `<h2>`、`<h3>` 建立清晰的标题层级
- `<div class="block">` 作为内容模块的容器
- 合理使用 `<span>` 和 `<label>` 区分不同类型的信息

### 2. CSS Grid布局应用
```css
/* 个人信息区域：4列不等宽网格 */
grid-template-columns: 20px 280px 60px 1fr;

/* 技能区域：3列等宽网格 */
grid-template-columns: repeat(3, 1fr);

/* 项目区域：2列布局 */
grid-template-columns: 60px 1fr;
```

### 3. 伪元素的妙用
- `::before` 创建装饰背景
- 时间轴的线条和圆点
- 圆形进度条的边框效果

### 4. 视觉效果技巧
- 渐变背景：`linear-gradient`
- 阴影效果：`box-shadow`
- 旋转变换：`transform: rotate()`
- 圆角设计：`border-radius`

### 5. 颜色方案
- 主色调：`#0ea5e9`（天蓝色）
- 辅助色：`#22d3ee`（青色）
- 背景色：`#fff`、`#f0fdff`
- 文字色：`#333`、`#888`

## 实践练习

### 基础任务
1. 修改个人信息，替换为自己的真实信息
2. 调整颜色方案，创建不同的视觉风格
3. 添加更多的教育经历和工作经历

### 进阶任务
1. 实现响应式布局，适配移动设备
2. 添加打印样式，优化打印效果
3. 使用动画效果，提升用户体验
4. 添加交互功能，如技能等级切换

### 挑战任务
1. 创建深色模式切换
2. 添加在线简历导出为PDF功能
3. 实现多语言支持
4. 添加数据可视化图表展示技能

## 常见问题

**Q: 如何调整简历的宽度？**
A: 修改 `.container` 的 `width` 属性，建议保持在800-1000px之间以保证最佳阅读体验。

**Q: 如何添加更多技能项？**
A: 在HTML中添加新的 `.item` 元素，并设置相应的 `level` 类名（level1-level4）。

**Q: 时间线样式可以自定义吗？**
A: 可以修改 `.time-line::before`（线条）和 `.time-line .item::before`（圆点）的样式。

**Q: 如何优化打印效果？**
A: 添加 `@media print` 样式，去除背景装饰，调整布局适合A4纸张。

## 扩展阅读

- [CSS Grid布局完全指南](https://css-tricks.com/snippets/css/complete-guide-grid/)
- [CSS伪元素艺术](https://css-tricks.com/pseudo-element-roundup/)
- [网页简历设计最佳实践](https://www.smashingmagazine.com/2020/03/resume-templates-websites/)
- [CSS渐变效果详解](https://css-tricks.com/css-basics-using-gradients/)
