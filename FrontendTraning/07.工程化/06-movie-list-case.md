# 06. 分页电影列表案例

## 学习目标

- 掌握webpack分包配置
- 理解开发环境跨域代理配置
- 学习动态导入的应用场景
- 实践模块化开发思维

> 效果展示地址：https://study.duyiedu.com/movie  
> 接口地址：https://app.apifox.com/link/project/2429576/apis/api-67925177

## 项目结构

```
movie-list/
├── public/
│   └── index.html
├── src/
│   ├── main.js                 # 入口文件
│   ├── cover/                  # 封面模块
│   │   ├── index.js
│   │   ├── index.module.less
│   │   └── assets/
│   ├── movie/                  # 电影模块
│   │   ├── index.js
│   │   ├── list/               # 电影列表
│   │   │   ├── index.js
│   │   │   └── index.module.less
│   │   ├── pager/              # 分页组件
│   │   │   ├── index.js
│   │   │   └── index.module.less
│   │   └── api/                # API接口
│   │       └── movie.js
│   └── global.less
├── webpack.config.js
├── babel.config.js
├── postcss.config.js
└── package.json
```

## 功能模块划分

项目采用模块化设计，主要包含以下功能模块：

- **封面模块**：页面初始展示，包含视频背景和标题
- **电影列表模块**：展示电影数据
- **分页模块**：提供分页导航功能
- **API模块**：封装数据请求接口

## Webpack分包配置

### 为什么需要分包？

如果站点中的所有依赖都打包到一个js文件中，会导致打包结果过大，影响页面加载性能。

实际开发中，页面初始时并不需要所有代码都参与运行。比如在这个项目中：
- 初始必须要运行的是**封面模块**（用户一开始必须看见）
- **电影模块**可以延迟加载（用户滚动后才需要）

### 静态导入 vs 动态导入

```javascript
// main.js
import './cover';        // 静态导入：表示该模块需要合并到主打包结果中
import('./movie');       // 动态导入：运行到此代码时才会去远程加载movie模块
```

**区别**：
- **静态导入**：模块会被打包到主bundle中，页面加载时同步加载
- **动态导入**：模块会形成独立的chunk，按需异步加载

### Webpack配置

```javascript
// webpack.config.js
module.exports = {
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'js/app-[contenthash:5].js',           // 主入口文件
    chunkFilename: 'js/chunk-[contenthash:5].js',    // 动态导入的chunk文件
    clean: true,
  },
  // ...
};
```

**说明**：
- `filename`: 配置主入口文件的输出名称
- `chunkFilename`: 配置动态导入模块的输出名称
- `[contenthash:5]`: 根据内容生成hash，便于缓存控制

### 打包结果

使用动态导入后，webpack会将movie模块单独打包：

```
dist/
├── js/
│   ├── app-a1b2c.js      # 主bundle（包含cover模块）
│   └── chunk-d3e4f.js    # movie模块的独立chunk
├── css/
└── index.html
```

浏览器运行时：
1. 首先加载主bundle（app-a1b2c.js）
2. 用户滚动到需要的位置时，动态加载movie模块的chunk（chunk-d3e4f.js）

这样可以**提升初始加载效率**，同时不影响后续模块的加载。

## 跨域代理配置

### 为什么需要跨域代理？

**大部分时候，为了安全，服务器都是不允许跨域访问的。**

生产环境部署时，通常采用同源部署，不存在跨域问题：

```
┌─────────────┐
│   Nginx     │
│  静态资源    │
└─────────────┘
     │
     └── 同源部署，无跨域
└─────────────┘
```

但在**开发阶段存在跨域问题**：

```
开发服务器(localhost:8080) ──→ API服务器(study.duyiedu.com)
              ❌ 跨域
```

因此，我们只需要**消除开发阶段的跨域问题**，便于查看效果。

### Webpack代理配置

```javascript
// webpack.config.js
module.exports = {
  devServer: {
    port: 8080,
    proxy: {
      '/api': {                    // 当请求地址以/api开头时，代理到目标地址
        target: 'https://study.duyiedu.com',  // 代理的目标地址
        changeOrigin: true,        // 更改请求头中的origin，避免跨域问题
      },
    },
  },
};
```

**参数说明**：
- `/api`: 匹配规则，当请求路径以/api开头时触发代理
- `target`: 代理的目标服务器地址
- `changeOrigin`: 修改请求头中的origin字段，使其与target同源

### API请求配置

```javascript
// src/api/movie.js
import axios from 'axios';

export async function getMovies(page, limit) {
  const resp = await axios.get('/api/movies', {  // 只需要写请求路径
    params: {
      page,
      size: limit,
    },
  });
  return resp.data;
}
```

**注意事项**：
- ❌ 错误写法：`axios.get('http://study.duyiedu.com/api/movies')`
- ✅ 正确写法：`axios.get('/api/movies')`

### 代理工作原理

```
浏览器请求: /api/movies
    ↓
webpack-dev-server检测到/api前缀
    ↓
代理转发: https://study.duyiedu.com/api/movies
    ↓
返回数据给浏览器
```

这样就实现了**开发环境和生产环境的统一**，开发时代理生效，生产环境由服务器处理。

## 核心代码实现

### 1. 主入口文件

```javascript
// src/main.js
import './cover';        // 静态依赖：封面模块必须初始加载
import('./movie');       // 动态依赖：电影模块按需加载
import './global.less';
```

**设计思路**：
- 封面模块使用静态导入，确保页面加载时立即显示
- 电影模块使用动态导入，延迟加载以提升性能

### 2. 封面模块

```javascript
// src/cover/index.js
import $ from 'jquery';
import styles from './index.module.less';
import videoUrl from '../assets/movie.mp4';
import audioUrl from '../assets/music.mp3';

function init() {
  const container = $('<div>').addClass(styles.container).appendTo('#app');
  
  // 创建视频元素
  const vdo = $('<video>')
    .prop('src', videoUrl)
    .prop('autoplay', true)
    .prop('loop', true)
    .addClass(styles.video)
    .appendTo(container);
  
  // 创建音频元素
  const aud = $('<audio>')
    .prop('src', audioUrl)
    .prop('autoplay', true)
    .prop('loop', true)
    .appendTo(container);

  // 创建标题
  $('<h1>').text('豆瓣电影').addClass(styles.title).appendTo(container);

  // 滚动控制播放
  $(window).on('scroll', function () {
    const scrollTop = document.documentElement.scrollTop;
    const vHeight = document.documentElement.clientHeight;
    if (scrollTop >= vHeight) {
      // 滚动超出首屏，暂停音视频
      vdo[0].pause();
      aud[0].pause();
    } else {
      // 在首屏内，播放音视频
      vdo[0].play();
      aud[0].play();
    }
  });
}

init();
```

**功能说明**：
- 创建全屏视频背景
- 添加背景音乐
- 根据滚动位置控制音视频播放，节省资源

### 3. 电影模块入口

```javascript
// src/movie/index.js
import { createMovieTags } from './list';
import { getMovies } from '../api/movie';
import { createPagers } from './pager';

async function init() {
  // 获取第一页数据，每页30条
  const resp = await getMovies(1, 30);
  createMovieTags(resp.data.movieList);           // 创建电影列表
  createPagers(1, 30, resp.data.movieTotal);       // 创建分页区域
}

init();
```

**设计思路**：
- 模块加载时自动初始化
- 调用API获取数据
- 分别渲染列表和分页组件

### 4. 电影列表模块

```javascript
// src/movie/list/index.js
import $ from 'jquery';
import styles from './index.module.less';

let container;

/**
 * 初始化函数，负责创建容器
 */
function init() {
  container = $('<div>').addClass(styles.container).appendTo('#app');
}

init();

/**
 * 根据传入的电影数组，创建元素，填充到容器中
 * @param {Array} movies 电影数组
 */
export function createMovieTags(movies) {
  const result = movies
    .map((m) => `
      <div>
        <a href="${m.url}" target="_blank">
          <img src="${m.cover}" alt="${m.title}">
        </a>
        <a href="${m.url}" target="_blank">
          <p class="${styles.title}">${m.title}</p>
        </a>
        <p class="${styles.rate}">${m.rate}</p>
      </div>
    `)
    .join('');
  container.html(result);
}
```

**设计思路**：
- 使用模块化CSS避免样式冲突
- 提供可复用的渲染函数
- 通过map和join高效生成HTML

### 5. 分页模块

```javascript
// src/movie/pager/index.js
import $ from 'jquery';
import styles from './index.module.less';
import { getMovies } from '@/api/movie';
import { createMovieTags } from '@/movie/list';

let container;

/**
 * 初始化函数，负责创建容器
 */
function init() {
  container = $('<div>').addClass(styles.pager).appendTo('#app');
}

init();

/**
 * 根据传入的页码、页容量、总记录数，创建分页区域的标签
 * @param {number} page 当前页码
 * @param {number} limit 页容量
 * @param {number} total 总记录数
 */
export function createPagers(page, limit, total) {
  container.empty();
  
  /**
   * 辅助函数：创建一个页码标签
   * @param {string} text 标签的文本
   * @param {string} status 标签状态：''普通 | 'disabled'禁用 | 'active'选中
   * @param {number} targetPage 目标页码
   */
  function createTag(text, status, targetPage) {
    const span = $('<span>').appendTo(container).text(text);
    const className = styles[status];
    span.addClass(className);
    
    // 只有普通状态才监听点击事件
    if (status === '') {
      span.on('click', async function () {
        // 1. 重新获取数据
        const resp = await getMovies(targetPage, limit);
        // 2. 重新生成列表
        createMovieTags(resp.data.movieList);
        // 3. 重新生成分页区域
        createPagers(targetPage, limit, resp.data.movieTotal);
      });
    }
  }
  
  const pageNumber = Math.ceil(total / limit); // 最大页码
  
  // 1. 创建首页标签
  createTag('首页', page === 1 ? 'disabled' : '', 1);
  
  // 2. 创建上一页标签
  createTag('上一页', page === 1 ? 'disabled' : '', page - 1);
  
  // 3. 创建数字页码标签
  const maxCount = 10; // 最大显示的数字页码数量
  let min = Math.floor(page - maxCount / 2);
  min < 1 && (min = 1);
  let max = min + maxCount - 1;
  max > pageNumber && (max = pageNumber);
  
  for (let i = min; i <= max; i++) {
    createTag(i, i === page ? 'active' : '', i);
  }

  // 4. 创建下一页标签
  createTag('下一页', page === pageNumber ? 'disabled' : '', page + 1);
  
  // 5. 创建尾页标签
  createTag('尾页', page === pageNumber ? 'disabled' : '', pageNumber);
}
```

**设计思路**：

1. **状态管理**：根据当前页码动态计算按钮状态
2. **页码算法**：保持当前页在显示页码的中间位置
3. **交互逻辑**：点击页码后重新获取数据并更新视图
4. **边界处理**：首页/尾页、第一页/最后一页的禁用状态

**页码计算逻辑**：
```javascript
// 保持当前页在中间位置
const maxCount = 10;  // 最多显示10个数字页码
let min = Math.floor(page - maxCount / 2);  // 计算起始页码
min < 1 && (min = 1);                        // 边界修正
let max = min + maxCount - 1;               // 计算结束页码
max > pageNumber && (max = pageNumber);    // 边界修正
```

### 6. API模块

```javascript
// src/api/movie.js
import axios from 'axios';

/**
 * 获取电影列表数据
 * @param {number} page 页码
 * @param {number} limit 每页数量
 * @returns {Promise} 返回电影数据
 */
export async function getMovies(page, limit) {
  const resp = await axios.get('/api/movies', {
    params: {
      page,
      size: limit,
    },
  });
  return resp.data;
}
```

**设计要点**：
- 封装axios请求，统一接口调用
- 使用async/await处理异步
- 返回数据便于上层使用

## Babel配置

```javascript
// babel.config.js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        useBuiltIns: 'usage',  // 按需引入polyfill
        corejs: 3,             // 使用core-js 3
      },
    ],
  ],
};
```

**配置说明**：
- `useBuiltIns: 'usage'`: 根据代码实际使用的API按需引入polyfill
- `corejs: 3`: 指定core-js版本，提供ES6+的API支持

## PostCSS配置

```javascript
// postcss.config.js
module.exports = () => ({
  plugins: ['cssnano', 'autoprefixer'],
});
```

**插件说明**：
- `cssnano`: 压缩优化CSS代码
- `autoprefixer`: 自动添加浏览器前缀

## 项目启动

### 安装依赖

```bash
npm install
```

### 开发模式

```bash
npm run serve
```

访问 http://localhost:8080 查看效果

### 生产构建

```bash
npm run build
```

## 关键技术点总结

### 1. 代码分割

- **静态导入**：同步加载，打包到主bundle
- **动态导入**：异步加载，形成独立chunk
- **应用场景**：优化首屏加载速度

### 2. 开发代理

- **问题**：开发环境存在跨域问题
- **解决**：webpack-dev-server代理
- **原理**：同源转发请求

### 3. 模块化设计

- **按功能划分模块**：cover、movie、list、pager
- **模块职责单一**：每个模块专注自己的功能
- **模块间协作**：通过导入导出实现交互

### 4. 交互设计

- **分页组件**：状态管理 + 事件处理
- **数据驱动**：根据数据动态渲染视图
- **用户体验**：禁用状态、加载反馈

## 扩展思考

1. **性能优化**：除了代码分割，还可以从哪些方面优化性能？
2. **错误处理**：API请求失败时应该如何处理？
3. **用户体验**：如何在数据加载时显示loading状态？
4. **缓存策略**：如何利用浏览器的缓存机制？
5. **打包优化**：如何进一步减小打包体积？

## 练习题

1. 尝试添加"搜索"功能，支持按电影名称搜索
2. 实现数据加载时的loading提示
3. 添加错误处理机制，当请求失败时显示错误信息
4. 优化分页算法，支持自定义显示的页码数量
5. 尝试将分页组件封装为可复用的组件
