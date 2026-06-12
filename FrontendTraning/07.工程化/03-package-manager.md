# 03. 包管理器

## 学习目标

- 理解包和包管理器的概念
- 掌握 npm 的基本使用
- 了解包的查找机制
- 掌握镜像源的配置
- 理解 package.json 的作用

## 1. 基本概念

### 1.1 什么是包？

包（package）是一个或多个 JS 模块的集合，它们共同完成某一类功能。可以简单地将每个工程视为一个包。

- **应用包**：包含完整应用的工程
- **库包**：提供特定功能的第三方库，供其他项目使用

### 1.2 什么是包管理器？

包管理器是管理包的工具，前端常见的包管理器包括：

- **npm**（Node Package Manager）- Node.js 官方包管理器
- **yarn** - Facebook 开发的快速、可靠的包管理器
- **cnpm** - 淘宝团队开发的 npm 镜像源工具
- **pnpm** - 高效的磁盘空间利用和快速安装的包管理器

**核心能力：**
- 轻松下载、升级和卸载包
- 自动管理包的依赖关系
- 统一管理项目配置

### 1.3 什么是 CLI？

CLI（Command Line Interface）是命令行工具，通过终端命令完成特定功能。

```bash
# 示例：使用 npm 命令
npm --version    # 查看 npm 版本
npm help         # 查看帮助信息
```

## 2. Node.js 查找包的顺序

当使用 `require("a")` 导入包时，Node.js 按以下顺序查找：

```js
require("a")
```

1. **查找内置模块**：检查 `a` 是否为 Node.js 内置模块
2. **查找当前目录**：检查当前目录 `node_modules` 中是否有 `a`
3. **向上递归查找**：依次查找上级目录的 `node_modules`，直到根目录

**示例结构：**
```
project/
  node_modules/
    a/          # 找到！使用这个
  src/
    index.js    # require("a") 从这里开始查找
```

## 3. npm 配置

### 3.1 查看当前镜像源

```bash
npm config get registry
```

### 3.2 配置淘宝镜像源

```bash
npm config set registry https://registry.npmmirror.com
```

### 3.3 恢复官方源

```bash
npm config set registry https://registry.npmjs.org/
```

### 3.4 其他常用配置

```bash
# 查看所有配置
npm config list

# 设置配置级别
npm config set init-author-name "Your Name"
npm config set init-license "MIT"
```

## 4. 项目初始化

### 4.1 初始化新项目

```bash
# 交互式初始化
npm init

# 使用默认配置快速初始化
npm init -y
```

### 4.2 package.json 文件

项目配置文件，记录项目信息和依赖关系：

```json
{
  "name": "my-project",              // 项目名称
  "version": "1.0.0",                 // 版本号
  "description": "项目描述",          // 描述
  "main": "index.js",                 // 入口文件
  "scripts": {                        // 脚本命令
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],                     // 关键词
  "author": "",                       // 作者
  "license": "ISC",                   // 许可证
  "dependencies": {                   // 生产环境依赖
    "qrcode": "^1.4.4"                // 依赖包：主版本不变，次版本和补丁版本可更新
  },
  "devDependencies": {                // 开发环境依赖
    "webpack": "^5.0.0"               // 仅开发时使用的工具
  }
}
```

## 5. 包的安装

### 5.1 本地安装

将包下载到当前目录的 `node_modules` 文件夹中，适用于绝大部分场景。

```bash
# 基本安装
npm install 包名
npm i 包名                # 简写形式

# 指定版本安装
npm install 包名@版本号
npm i 包名@1.2.3

# 安装为开发依赖
npm install -D 包名
npm install --save-dev 包名

# 精确安装版本（不使用 ^ 符号）
npm install --save-exact 包名
npm install -E 包名
```

### 5.2 全局安装

将包安装到全局目录，用于需要全局命令的工具。

```bash
# 全局安装
npm install -g 包名
npm install --global 包名

# 查看全局安装位置
npm root -g

# 查看全局安装的包
npm list -g --depth=0
```

### 5.3 还原依赖

根据 `package.json` 安装所有依赖：

```bash
# 安装所有依赖
npm install
npm i

# 仅安装生产依赖
npm install --production
```

## 6. 包的卸载

### 6.1 本地卸载

```bash
npm uninstall 包名
npm un 包名               # 简写形式
npm remove 包名
```

### 6.2 全局卸载

```bash
npm uninstall -g 包名
npm un -g 包名
```

## 7. 查看包信息

### 7.1 查看包的所有版本

```bash
npm view 包名 versions
npm v 包名 versions       # 简写形式
```

### 7.2 查看包的详细信息

```bash
npm view 包名
npm info 包名
```

### 7.3 查看已安装的包

```bash
# 查看本地安装的包
npm list
npm ls

# 查看全局安装的包
npm list -g

# 查看顶级依赖
npm list --depth=0
```

## 8. 版本号规则

npm 使用语义化版本号（SemVer）：`主版本.次版本.补丁版本`

```json
{
  "dependencies": {
    "package": "^1.2.3",    // 允许 1.x.x 的更新，不允许 2.0.0
    "package": "~1.2.3",    // 允许 1.2.x 的更新，不允许 1.3.0
    "package": "1.2.3",     // 精确版本
    "package": "*",         // 任意版本
    "package": "latest"     // 最新版本
  }
}
```

**符号说明：**
- `^`：主版本号不变，次版本和补丁版本可更新（默认）
- `~`：主版本和次版本不变，补丁版本可更新
- 无符号：精确匹配指定版本
- `*` 或 `x`：通配符，匹配任意版本

## 9. 常用命令速查表

| 操作 | 命令 | 说明 |
|------|------|------|
| 初始化 | `npm init -y` | 快速创建 package.json |
| 安装依赖 | `npm install` | 安装所有依赖 |
| 安装包 | `npm i 包名` | 本地安装 |
| 开发依赖 | `npm i -D 包名` | 安装开发依赖 |
| 全局安装 | `npm i -g 包名` | 全局安装 |
| 卸载包 | `npm un 包名` | 本地卸载 |
| 查看版本 | `npm view 包名 versions` | 查看所有版本 |
| 更新包 | `npm update` | 更新依赖 |

## 10. 最佳实践

1. **使用 `-D` 安装开发依赖**：区分生产依赖和开发依赖
2. **定期更新依赖**：`npm update` 保持依赖安全
3. **使用 lock 文件**：提交 `package-lock.json` 确保版本一致
4. **清理缓存**：遇到安装问题时使用 `npm cache clean --force`
5. **检查过时包**：使用 `npm outdated` 查看可更新的包

## 参考资源

- [npm 官方网站](https://www.npmjs.com/)
- [npm 完整命令文档](https://docs.npmjs.com/cli/v7/commands)
- [语义化版本规范](https://semver.org/lang/zh-CN/)