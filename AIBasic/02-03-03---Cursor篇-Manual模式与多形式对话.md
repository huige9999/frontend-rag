# [Cursor篇] Manual模式与多形式对话

## 课程概述

本课程介绍 Cursor 编辑器中的 Manual 模式以及多种对话形式的使用方法，帮助开发者更精准地控制 AI 辅助编程的过程。

## Manual模式详解

### 什么是Manual模式

Manual 模式是一种专注且**可控的编辑模式**，适用于你已经明确知道要修改什么文件、要怎么改的情况。

### 与Agent模式的核心区别

- **上下文处理**：根据用户提供的上下文进行操作，不会自动探索代码库
- **终端命令**：不会运行终端命令
- **文件控制**：完全依赖用户提供的具体指令和上下文（如通过 `@文件名` 显式指定要修改的文件）
- **修改范围**：Manual模式用户明确知道要修改哪些文件，所有要修改的文件都由用户指定。Agent模式会自动根据AI的规划，修改一些相关的文件。

### 适用场景

1. 想要 AI 修改某些文件或者某些函数（作为作者目的是非常明确的）
2. 你不希望 AI 自作主张修改项目的其它部分
3. 希望 AI 扮演的是执行器，我们自己负责思考和决策

### Manual模式工作流

1. **理解请求**：在聊天框中输入你想让 AI 做的任务描述
2. **添加上下文**：使用 `@文件名` 的方式，在请求中明确指出你希望编辑的文件
3. **规划修改**：AI 会根据你**提供的上下文**，给出修改建议。你可以预览修改内容
4. **执行修改**：确认无误后，应用这些修改。任务完成后，Manual 模式不会再进行任何自动处理

### 三种模式对比

| 对比维度                | Ask 模式                   | Manual 模式                             | Agent 模式                           |
| ----------------------- | -------------------------- | --------------------------------------- | ------------------------------------ |
| **核心定位**            | 只读提问、理解代码         | 精准编辑、按指令操作                    | 全自动 AI 开发助手                   |
| **主要用途**            | 学习代码库、探索项目结构   | 精确修改已知位置的代码                  | 实现复杂需求、规划并修改项目         |
| **是否会修改代码**      | ❌ 不会修改                 | ✅ 修改指定的文件                        | ✅ 修改多个文件甚至执行命令           |
| **是否需要手动 @ 文件** | ✅ 推荐使用                 | ✅ 必须使用                              | ❌ 自动探索上下文                     |
| **是否支持终端命令**    | ❌ 不支持                   | ❌ 不支持                                | ✅ 支持（如安装依赖）                 |
| **控制权归属**          | 用户主导提问，AI仅回复     | 用户全权控制修改范围                    | AI 主导执行，用户可监督              |
| **适合人群**            | 初学者、调研者、阅读代码者 | 想要精准控制变更的开发者                | 忙碌或希望 AI 自动执行任务的高级用户 |
| **推荐场景**            | 看不懂项目、需要查逻辑     | 想改一个函数/某段代码                   | 想让 AI 做一整个功能模块             |
| **修改预览与确认**      | ❌ 无修改                   | ✅ 提供预览                              | ✅ 自动修改并汇报结果                 |
| **典型行为**            | "这段代码是做什么的？"     | "请把 @login.ts 中的密码长度改成 12 位" | "实现一个带搜索功能的用户列表页面"   |

## 多形式对话

### 内联聊天（Inline Chat）

在 Cursor 中，你可以使用以下快捷键打开命令输入栏（Prompt Bar）：
- **macOS**：`Cmd K`
- **Windows/Linux**：`Ctrl K`

#### 功能特点
- 输入自然语言描述来提问
- 使用 @ 符号引用其他内容
- 对生成的结果进行多次优化
- 内联在文件中，不是单独窗口

#### 使用场景

**1. 生成新代码**
- 不选中任何代码，直接按下快捷键
- 输入你的需求描述
- Cursor 会根据上下文生成代码

**2. 修改现有代码**
- 选中要修改的代码
- 按下快捷键并描述修改需求
- AI 会按照你的要求进行修改

### 终端中聊天

打开终端，然后`Cmd K` 开启聊天栏，按 `Esc` 关闭内联聊天，按回车键 `Enter` 执行。

#### 使用示例
1. 查找大文件
2. 批量重命名
3. 进程管理

## 配套代码示例

### 选择排序算法示例

```javascript
/**
 * 选择排序算法
 * 时间复杂度: O(n²)
 * 空间复杂度: O(1)
 * @param {Array} arr - 需要排序的数组
 * @returns {Array} - 排序后的数组
 */
function selectionSort(arr) {
    // 创建数组副本，避免修改原数组
    const array = [...arr];
    const length = array.length;
    
    // 外层循环，每次找到最小元素放到已排序部分的末尾
    for (let i = 0; i < length - 1; i++) {
        // 假设当前位置的元素是最小的
        let minIndex = i;
        
        // 内层循环，在未排序部分找到最小元素
        for (let j = i + 1; j < length; j++) {
            // 如果找到更小的元素，更新最小索引
            if (array[j] < array[minIndex]) {
                minIndex = j;
            }
        }
        
        // 如果最小元素不在当前位置，则交换
        if (minIndex !== i) {
            // 使用解构赋值进行交换
            [array[i], array[minIndex]] = [array[minIndex], array[i]];
        }
    }
    
    return array;
}

// 测试代码
function testSelectionSort() {
    console.log("=== 选择排序测试 ===");
    
    // 测试用例1：普通数组和负数
    const arr1 = [64, 34, 25, 12, 22, 11, 90, -5, 10, -3, 8, -1, 0, 4];
    console.log("原始数组:", arr1);
    console.log("排序后:", selectionSort(arr1));
    
    // 测试用例2：特殊情况数组（空数组、单元素数组、已排序数组）
    const arr2 = [];
    console.log("\n原始数组(空数组):", arr2);
    console.log("排序后:", selectionSort(arr2));
    console.log("\n原始数组(单元素):", [42]);
    
    console.log("排序后:", selectionSort([42]));
    console.log("\n原始数组(已排序):", [1, 2, 3, 4, 5]);
    console.log("排序后:", selectionSort([1, 2, 3, 4, 5]));
    
    // 测试用例3：包含重复元素的大数组
    const arr3 = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, ...Array.from({length: 20}, () => Math.floor(Math.random() * 100))];
    console.log("\n原始数组:", arr3);
    console.log("排序后:", selectionSort(arr3));
}

// 运行测试
testSelectionSort();
```

### 插入排序算法示例

```javascript
// 插入排序算法
function insertionSort(array) {
    const length = array.length;
    
    // 从第二个元素开始遍历
    for (let i = 1; i < length; i++) {
        // 保存当前要插入的元素
        const current = array[i];
        let j = i - 1;
        
        // 将比当前元素大的元素都向后移动一位
        while (j >= 0 && array[j] > current) {
            array[j + 1] = array[j];
            j--;
        }
        
        // 在正确的位置插入当前元素
        array[j + 1] = current;
    }
    
    return array;
}

// 测试插入排序
function testInsertionSort() {
    console.log("\n=== 插入排序测试 ===");
    
    // 测试用例1：普通数组
    const arr1 = [64, 34, 25, 12, 22, 11, 90];
    console.log("原始数组:", arr1);
    console.log("排序后:", insertionSort(arr1));
    
    // 测试用例2：已排序数组
    const arr2 = [1, 2, 3, 4, 5];
    console.log("\n原始数组:", arr2);
    console.log("排序后:", insertionSort(arr2));
    
    // 测试用例3：逆序数组
    const arr3 = [5, 4, 3, 2, 1];
    console.log("\n原始数组:", arr3);
    console.log("排序后:", insertionSort(arr3));
    
    // 测试用例4：包含重复元素的数组
    const arr4 = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5];
    console.log("\n原始数组:", arr4);
    console.log("排序后:", insertionSort(arr4));
    
    // 测试用例5：空数组
    const arr5 = [];
    console.log("\n原始数组:", arr5);
    console.log("排序后:", insertionSort(arr5));
}

// 运行测试
testInsertionSort();
```

## 课程要点总结

1. **Manual模式的核心价值**：提供精确可控的代码修改方式，用户明确指定修改范围
2. **模式选择策略**：根据任务复杂度和控制需求选择合适的工作模式
3. **高效对话技巧**：掌握内联聊天和终端聊天的使用场景
4. **实践应用**：通过算法代码示例理解 AI 辅助编程的最佳实践

## 参考资源

- [Cursor官方文档 - Manual模式](https://docs.cursor.com/chat/manual#example-use-cases)
