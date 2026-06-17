# frontend-node 苏格拉底式提问

## 1. frontend-node/4-9. 跨域之JSONP 有使用exports.logger = defaultLogger;吗？如果有 是怎么用的？直接当普通日志console.log那样调用吗？


## 2. frontend-node/4-9. 跨域之JSONP 假如没有app.use(express.json()); 那么请求体格式如果是application/json，express会自动解析吗？如果不解析，req.body会是什么？

## 3. frontend-node/4-9. 跨域之JSONP // 映射public目录中的静态资源
const path = require("path");
const staticRoot = path.resolve(__dirname, "../public");
app.use(express.static(staticRoot)); 静态资源中间件是如何命中请求的？换句话说 在哪配置 啥样的前缀请求会走到这个中间件？


### 4. frontend-node/4-9. 跨域之JSONP // 加入cookie-parser 中间件
// 加入之后，会在req对象中注入cookies属性，用于获取所有请求传递过来的cookie
// 加入之后，会在res对象中注入cookie方法，用于设置cookie
const cookieParser = require("cookie-parser");
app.use(cookieParser());

// 应用token中间件
app.use(require("./tokenMiddleware"));

// 解析 application/x-www-form-urlencoded 格式的请求体
app.use(express.urlencoded({ extended: true }));

// 解析 application/json 格式的请求体
app.use(express.json()); 
像这种没有前缀url定义的中间件，所有请求都会走到这个中间件吗？