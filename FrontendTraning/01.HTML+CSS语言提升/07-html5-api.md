# 第07章 HTML5 API

## 本章目标

- 掌握classList API的使用
- 理解localStorage的基本概念和应用场景
- 学会使用localStorage进行数据存储和读取
- 能够运用HTML5 API解决实际开发问题

## 7.1 classList API

### 7.1.1 什么是classList

classList是HTML5提供的一个用于操作元素类名的API，它提供了一组方法和属性来方便地添加、删除、切换和检查元素的CSS类。

### 7.1.2 classList的常用方法

#### add() - 添加类名
```javascript
element.classList.add('className');
```

#### remove() - 删除类名
```javascript
element.classList.remove('className');
```

#### toggle() - 切换类名
```javascript
element.classList.toggle('className');
// 如果类名存在则删除，不存在则添加
```

#### contains() - 检查类名是否存在
```javascript
element.classList.contains('className');
// 返回true或false
```

### 7.1.3 实际应用示例

下面是一个选项卡切换的案例，展示如何使用classList API来动态控制元素的类名：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>classList选项卡示例</title>
  <style>
    .menu {
      display: flex;
      list-style: none;
      justify-content: center;
    }
    .menu li {
      margin: 0 10px;
      cursor: pointer;
      padding: 10px 20px;
      border: 1px solid #ddd;
      transition: all 0.3s;
    }
    .menu li.active {
      color: #fff;
      background-color: #f40;
      border-color: #f40;
    }
  </style>
</head>
<body>
  <ul class="menu">
    <li>item1</li>
    <li class="active">item2</li>
    <li>item3</li>
    <li>item4</li>
    <li>item5</li>
  </ul>
  <script>
    // 点击li时，切换active到被点击的li上
    var lis = document.querySelectorAll('.menu li');
    
    for (var i = 0; i < lis.length; i++) {
      lis[i].onclick = function () {
        // 去掉之前被选中的li的active类名
        var beforeActive = document.querySelector('.menu .active');
        beforeActive.classList.remove('active');
        
        // 给当前被点击的li添加active类名
        this.classList.add('active');
      };
    }
  </script>
</body>
</html>
```

### 7.1.4 代码解析

1. **获取元素集合**：使用`querySelectorAll()`获取所有菜单项
2. **绑定点击事件**：通过循环为每个li元素绑定onclick事件
3. **移除旧状态**：找到之前带有active类名的元素，使用`classList.remove()`移除
4. **添加新状态**：使用`classList.add()`为当前点击的元素添加active类名

## 7.2 localStorage API

### 7.2.1 什么是localStorage

localStorage是HTML5提供的一种本地存储方案，它允许浏览器在本地保存键值对数据。这些数据没有过期时间，除非手动删除，否则会一直存在。

### 7.2.2 localStorage的特点

- **持久性存储**：数据不会因为浏览器关闭而消失
- **域名隔离**：不同域名下的localStorage数据相互独立
- **容量限制**：通常为5MB左右
- **字符串存储**：只支持存储字符串类型数据

### 7.2.3 localStorage的常用方法

#### setItem() - 存储数据
```javascript
localStorage.setItem('key', 'value');
```

#### getItem() - 读取数据
```javascript
var value = localStorage.getItem('key');
```

#### removeItem() - 删除数据
```javascript
localStorage.removeItem('key');
```

#### clear() - 清空所有数据
```javascript
localStorage.clear();
```

### 7.2.4 存储复杂数据类型

由于localStorage只能存储字符串，要存储对象或数组，需要使用JSON进行转换：

```javascript
// 存储对象
var obj = { name: '张三', age: 25 };
localStorage.setItem('user', JSON.stringify(obj));

// 读取对象
var userData = JSON.parse(localStorage.getItem('user'));
console.log(userData.name); // 输出：张三
```

### 7.2.5 实际应用示例

下面是一个表单数据持久化的案例，展示如何使用localStorage保存和加载表单数据：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>localStorage表单示例</title>
  <style>
    .form {
      width: 300px;
      margin: 50px auto;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 5px;
    }
    .item {
      margin: 1em 0;
    }
    label {
      display: inline-block;
      width: 60px;
      font-weight: bold;
    }
    input[type="text"],
    input[type="number"] {
      width: 200px;
      padding: 5px;
      border: 1px solid #ddd;
      border-radius: 3px;
    }
  </style>
</head>
<body>
  <div class="form">
    <h3>用户信息</h3>
    <div class="item">
      <label>姓名</label>
      <input id="txtName" type="text" placeholder="请输入姓名" />
    </div>
    <div class="item">
      <label>年龄</label>
      <input id="txtAge" type="number" placeholder="请输入年龄" />
    </div>
    <div class="item">
      <label>性别</label>
      <label>
        <input checked id="sexMale" name="sex" type="radio" />
        男
      </label>
      <label>
        <input id="sexFemale" name="sex" type="radio" />
        女
      </label>
    </div>
  </div>
  <script>
    // 获取表单元素
    var txtName = document.querySelector('#txtName');
    var txtAge = document.querySelector('#txtAge');
    var sexMale = document.querySelector('#sexMale');
    var sexFemale = document.querySelector('#sexFemale');
    
    // 为所有输入框绑定input事件，实时保存数据
    var inputs = document.querySelectorAll('input');
    for (var i = 0; i < inputs.length; i++) {
      inputs[i].addEventListener('input', saveFormData);
    }
    
    /**
     * 保存表单数据到localStorage
     * 将表单信息封装成对象，转换为JSON字符串后存储
     */
    function saveFormData() {
      var formData = {
        name: txtName.value,
        age: txtAge.value ? +txtAge.value : 0,
        sex: sexMale.checked ? '男' : '女',
      };
      
      // 将对象转换为JSON字符串并存储
      var json = JSON.stringify(formData);
      localStorage.setItem('userForm', json);
      
      console.log('数据已保存：', formData);
    }

    /**
     * 从localStorage加载表单数据
     * 读取JSON字符串，解析为对象后填充到表单
     */
    function loadFormData() {
      var json = localStorage.getItem('userForm');
      
      // 如果没有保存的数据，直接返回
      if (!json) {
        return;
      }
      
      // 解析JSON字符串为对象
      var formData = JSON.parse(json);
      
      // 将数据填充到表单
      txtName.value = formData.name || '';
      txtAge.value = formData.age || '';
      
      // 根据性别设置单选按钮状态
      if (formData.sex === '男') {
        sexMale.checked = true;
      } else {
        sexFemale.checked = true;
      }
      
      console.log('数据已加载：', formData);
    }

    // 页面加载时自动填充数据
    loadFormData();
  </script>
</body>
</html>
```

### 7.2.6 代码解析

1. **元素获取**：使用`querySelector()`获取各个表单元素
2. **事件绑定**：为所有input元素绑定input事件，实现实时保存
3. **数据保存**：
   - 将表单数据封装成对象
   - 使用`JSON.stringify()`将对象转换为字符串
   - 使用`localStorage.setItem()`存储数据
4. **数据加载**：
   - 使用`localStorage.getItem()`读取数据
   - 使用`JSON.parse()`将字符串转换回对象
   - 将数据填充到对应的表单元素

## 7.3 课堂练习

### 练习1：选项卡切换

**要求**：
- 实现一个选项卡组件
- 点击选项卡时，切换active类名
- 当前选中的选项卡需要高亮显示

**提示**：
- 使用classList的add()和remove()方法
- 注意处理初始状态的active类名

### 练习2：表单数据持久化

**要求**：
- 创建一个用户信息表单
- 实时保存用户输入的数据到localStorage
- 刷新页面后能够恢复之前的数据

**提示**：
- 使用input事件监听器实现实时保存
- 使用JSON进行数据转换
- 页面加载时调用load函数恢复数据

## 7.4 本章小结

### 知识要点

1. **classList API**
   - 提供了便捷的类名操作方法
   - 常用方法：add()、remove()、toggle()、contains()
   - 比传统的className属性更加安全和便捷

2. **localStorage API**
   - 用于在浏览器本地持久化存储数据
   - 只支持字符串类型，需要使用JSON处理复杂数据
   - 常用方法：setItem()、getItem()、removeItem()、clear()

3. **实际应用**
   - classList适用于动态样式切换场景
   - localStorage适用于数据持久化、用户偏好设置等场景

### 注意事项

1. **浏览器兼容性**：HTML5 API在现代浏览器中都得到了良好支持
2. **数据安全**：localStorage中的数据可以被用户查看和修改，不适合存储敏感信息
3. **存储限制**：注意localStorage的容量限制，避免存储过多数据
4. **错误处理**：在实际开发中需要添加适当的错误处理机制

## 7.5 课后作业

1. 使用classList API实现一个手风琴效果
2. 使用localStorage创建一个简易的待办事项应用
3. 思考：在实际项目中，你会在哪些场景使用这些API？

---

**课程代码路径**：`01. HTML+CSS语言提升/07. HTML5API/`

**相关资源**：
- [MDN - Document.classList](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/classList)
- [MDN - Window.localStorage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage)