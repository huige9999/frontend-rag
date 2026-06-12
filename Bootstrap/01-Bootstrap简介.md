# Bootstrap 简介

## 课程简介

本节课介绍 Bootstrap 框架的基本概念，以及如何在项目中引入 Bootstrap 的 CSS 和 JS 文件。Bootstrap 是一个用于快速开发响应式网站的前端框架，提供了丰富的组件和工具类。

## 关键知识点

- Bootstrap 当前使用的是 **4.3.1 版本**
- 引入 Bootstrap 需要三个部分：
  1. **CSS 文件**：通过 `<link>` 标签引入
  2. **jQuery**：Bootstrap 的 JS 依赖
  3. **Popper.js**：用于提示框、弹出框等组件的定位
  4. **Bootstrap JS**：Bootstrap 的交互功能
- 必须设置 `viewport` 元信息标签以支持响应式布局

## 关键代码示例

### 1. viewport 设置

响应式页面必须在 `<head>` 中添加 viewport meta 标签：

```html
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```

- `width=device-width`：页面宽度跟随设备宽度
- `initial-scale=1`：初始缩放比例为 1
- `shrink-to-fit=no`：禁止页面收缩以适应屏幕（iOS 专用）

### 2. 引入 Bootstrap CSS

```html
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
  integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
  crossorigin="anonymous">
```

### 3. 引入依赖 JS 文件

```html
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
  integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
  crossorigin="anonymous"></script>

<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
  integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1"
  crossorigin="anonymous"></script>

<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
  integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM"
  crossorigin="anonymous"></script>
```

> 注意：引入顺序为 jQuery -> Popper.js -> Bootstrap JS

## 效果说明

- 正确引入 Bootstrap 后，页面即可使用 Bootstrap 提供的所有 CSS 类（如容器、栅格、按钮、表单等）
- `integrity` 属性用于子资源完整性校验，确保 CDN 文件未被篡改
- `crossorigin="anonymous"` 允许跨域请求但不发送证书
