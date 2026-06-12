# 4-9 跨域之JSONP

## 知识要点

- 浏览器的同源策略限制了跨域请求
- JSONP 利用 `<script>` 标签不受同源策略限制的特点实现跨域
- JSONP 只支持 GET 请求
- JSONP 需要服务器配合返回指定格式的 JS 代码
- JSONP 存在安全隐患，不推荐在生产环境中使用

## JSONP 原理

1. 客户端创建一个 `<script>` 标签，src 指向跨域的 API
2. 客户端在 URL 中传递一个回调函数名（如 `callback=handleResponse`）
3. 服务器收到请求后，将数据包装在回调函数中返回：`handleResponse(data)`
4. 浏览器接收到 JS 代码后执行，触发回调函数

## JSONP 的局限性

1. **只能完成 GET 请求** -- 因为 script 标签只能发 GET 请求
2. **会打乱服务器的消息格式** -- 服务器需要响应 JS 代码而非标准 JSON
3. **存在安全风险** -- 容易受到 XSS 攻击

## 本节代码

本节代码结构与 4-8 一致，主要讲解 JSONP 的跨域原理。JSONP 是一种较早的跨域方案，现已逐渐被 CORS 取代。
