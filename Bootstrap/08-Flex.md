# 08 - Flex

## 课程简介

本节课全面学习 Bootstrap 4 中的 **Flex 弹性盒模型** 工具类，包括启用弹性盒、排列方向、主轴对齐、交叉轴对齐、元素自身对齐、填满、伸缩值、自动间距、换行、排序以及多行对齐等内容。Flex 是 Bootstrap 4 布局的核心，掌握它对理解和运用栅格系统至关重要。

---

## 一、启用弹性盒

### 关键知识点

- 使用 `.d-flex` 将元素设置为块级弹性容器
- 使用 `.d-inline-flex` 将元素设置为行内弹性容器
- 响应式写法：`.d-{breakpoint}-flex`、`.d-{breakpoint}-inline-flex`

### 关键代码示例

```html
<div class="d-flex">块级弹性盒子</div>
<div class="d-inline-flex">行内弹性盒子</div>
<div class="d-lg-flex">响应式弹性盒子（大屏启用）</div>
```

---

## 二、子元素排列方向

### 关键知识点

- `.flex-row`：正序水平排列（默认值）
- `.flex-row-reverse`：倒序水平排列
- `.flex-column`：正序垂直排列
- `.flex-column-reverse`：倒序垂直排列
- 响应式写法：`.flex-{breakpoint}-row/column/row-reverse/column-reverse`

### 关键代码示例

```html
<div class="d-flex flex-row">
    <div>第1列</div><div>第2列</div><div>第3列</div>
</div>
<div class="d-flex flex-row-reverse">
    <div>第1列</div><div>第2列</div><div>第3列</div>
</div>
<div class="d-flex flex-column">
    <div>第1列</div><div>第2列</div><div>第3列</div>
</div>
<div class="d-flex flex-column-reverse">
    <div>第1列</div><div>第2列</div><div>第3列</div>
</div>
```

---

## 三、主轴对齐（水平对齐）

### 关键知识点

- `.justify-content-start`：左对齐
- `.justify-content-end`：右对齐
- `.justify-content-center`：居中对齐
- `.justify-content-between`：两端对齐
- `.justify-content-around`：分散居中对齐
- 响应式写法：`.justify-content-{breakpoint}-start/end/center/between/around`

### 关键代码示例

```html
<div class="d-flex justify-content-start">左对齐</div>
<div class="d-flex justify-content-end">右对齐</div>
<div class="d-flex justify-content-center">居中对齐</div>
<div class="d-flex justify-content-between">两端对齐</div>
<div class="d-flex justify-content-around">分散居中对齐</div>
<div class="d-flex justify-content-lg-around">响应式分散居中</div>
```

---

## 四、交叉轴对齐（垂直对齐）

### 关键知识点

- `.align-items-start`：顶对齐
- `.align-items-end`：底对齐
- `.align-items-center`：中间对齐
- `.align-items-baseline`：基线对齐
- `.align-items-stretch`：拉伸填满（子元素无高度时占满容器高度）
- 响应式写法：`.align-items-{breakpoint}-start/end/center/baseline/stretch`

### 关键代码示例

```html
<div class="d-flex align-items-start" style="height: 100px;">顶对齐</div>
<div class="d-flex align-items-end" style="height: 100px;">底对齐</div>
<div class="d-flex align-items-center" style="height: 100px;">居中对齐</div>
<div class="d-flex align-items-baseline" style="height: 100px;">基线对齐</div>
<div class="d-flex align-items-stretch" style="height: 100px;">拉伸填满</div>
```

---

## 五、元素自身对齐

### 关键知识点

- `.align-self-start`、`.align-self-end`、`.align-self-center`、`.align-self-baseline`、`.align-self-stretch`
- 设置单个子元素的交叉轴对齐方式，会覆盖父级的 `align-items` 设置
- 响应式写法：`.align-self-{breakpoint}-start/end/center/baseline/stretch`

### 关键代码示例

```html
<div class="d-flex" style="height: 100px;">
    <div class="align-self-start">顶对齐</div>
    <div class="align-self-end">底对齐</div>
    <div class="align-self-center">居中对齐</div>
    <div class="align-self-baseline">基线对齐</div>
    <div class="align-self-stretch">拉伸填满</div>
</div>
```

---

## 六、填满（Fill）

### 关键知识点

- `.flex-fill`：让子元素等分剩余空间
- 响应式写法：`.flex-{breakpoint}-fill`

### 关键代码示例

```html
<div class="d-flex">
    <div class="flex-fill">第1列</div>
    <div class="flex-fill">第2列</div>
    <div class="flex-fill">第3列</div>
</div>
```

---

## 七、伸缩值（Grow / Shrink）

### 关键知识点

- `.flex-grow-0`：不扩展（默认）；`.flex-grow-1`：扩展占满剩余空间
- `.flex-shrink-0`：不收缩；`.flex-shrink-1`：允许收缩（默认）
- 响应式写法：`.flex-{breakpoint}-{grow|shrink}-0/1`

### 关键代码示例

```html
<!-- grow：第1列扩展占满剩余空间 -->
<div class="d-flex">
    <div class="flex-grow-1">第1列（扩展）</div>
    <div>第2列</div>
    <div>第3列</div>
</div>

<!-- shrink：第2列允许收缩 -->
<div class="d-flex">
    <div class="w-100">第1列（宽100%）</div>
    <div class="flex-shrink-1">第2列（可收缩）</div>
</div>
```

---

## 八、自动间距（Auto Margins）

### 关键知识点

- `.mr-auto`：右侧自动间距，将后续元素推到最右
- `.ml-auto`：左侧自动间距，将当前元素推到最右
- `.mb-auto` / `.mt-auto`：垂直方向的自动间距

### 关键代码示例

```html
<!-- 水平方向 -->
<div class="d-flex">
    <div class="mr-auto">第1列</div>
    <div>第2列</div>
    <div>第3列</div>
</div>
<div class="d-flex">
    <div>第1列</div>
    <div>第2列</div>
    <div class="ml-auto">第3列</div>
</div>

<!-- 垂直方向 -->
<div class="d-flex flex-column" style="height: 220px;">
    <div class="mb-auto col-2">第1列</div>
    <div class="col-2">第2列</div>
    <div class="col-2">第3列</div>
</div>
```

---

## 九、换行（Wrap）

### 关键知识点

- `.flex-wrap`：允许换行
- `.flex-nowrap`：不换行（默认）
- `.flex-wrap-reverse`：反向换行
- 响应式写法：`.flex-{breakpoint}-wrap/nowrap/wrap-reverse`

### 关键代码示例

```html
<div class="d-flex flex-wrap-reverse">
    <div class="col-1">第1列</div>
    <div class="col-1">第2列</div>
    <!-- 超出容器宽度后自动换行 -->
</div>
```

---

## 十、排序（Order）

### 关键知识点

- `.order-0` ~ `.order-12`：设置子元素的排列顺序，数字越小越靠前
- 响应式写法：`.order-{breakpoint}-0~12`

### 关键代码示例

```html
<div class="d-flex">
    <div class="order-2">第1列</div>
    <div class="order-6">第2列</div>
    <div class="order-3">第3列</div>
    <div class="order-1">第4列</div>
    <div class="order-0">第5列（最靠前）</div>
</div>
```

---

## 十一、多行对齐（Align Content）

### 关键知识点

- **仅对多行有效**，单行无效果
- `.align-content-start`：顶对齐
- `.align-content-end`：底对齐
- `.align-content-center`：中间对齐
- `.align-content-between`：上下两端对齐
- `.align-content-around`：上下分散对齐
- `.align-content-stretch`：子元素无高度时占满父级高度
- 响应式写法：`.align-content-{breakpoint}-start/end/center/between/around/stretch`

### 关键代码示例

```html
<div class="d-flex flex-wrap align-content-stretch" style="height: 200px;">
    <div class="col-1">第1列</div>
    <div class="col-1">第2列</div>
    <!-- 多行子元素会按指定方式垂直对齐 -->
</div>
```
