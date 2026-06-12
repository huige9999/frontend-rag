# Vuex经典案例：用户认证系统

## 章节概述

本章节通过一个完整的用户认证系统案例，综合运用Vuex的核心概念，包括：
- Vuex模块化（Modules）
- 命名空间（Namespaced）
- Getters的使用
- Actions处理异步操作
- Vuex与Vue Router的集成
- 路由导航守卫

## 案例架构

### 系统架构图

```
┌─────────────────────────────────────────────────────────┐
│                        用户认证系统                        │
├─────────────────────────────────────────────────────────┤
│  ┌──────────┐      ┌──────────┐      ┌──────────┐      │
│  │  Login   │──────│ Loading  │──────│   User   │      │
│  │  登录页   │      │  加载页   │      │  个人中心 │      │
│  └──────────┘      └──────────┘      └──────────┘      │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│                    Vuex Store                            │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────┐   │
│  │         loginUser Module (命名空间模块)           │   │
│  ├─────────────────────────────────────────────────┤   │
│  │  • state: loading, user                          │   │
│  │  • getters: status                               │   │
│  │  • mutations: setLoading, setUser                │   │
│  │  • actions: login, whoAmI, loginOut              │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│                     Router                               │
├─────────────────────────────────────────────────────────┤
│  • 全局前置守卫（beforeEach）处理鉴权逻辑                   │
│  • 根据用户登录状态控制路由访问                             │
└─────────────────────────────────────────────────────────┘
```

### 鉴权流程图

```
┌─────────────────────────────────────────────────────────┐
│                    用户访问需要鉴权的页面                   │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│              路由守卫检查 meta.auth 属性                   │
└─────────────────────────────────────────────────────────┘
                         ↓
        ┌────────────────────────────────┐
        │    获取 loginUser/status       │
        │    (通过getter计算登录状态)     │
        └────────────────────────────────┘
                         ↓
        ┌────────────────┬────────────────┬────────────┐
        ↓                 ↓                ↓            ↓
   status =       status =        status =     其他情况
   "loading"      "login"        "unlogin"
        ↓                 ↓                ↓
  跳转到Loading    允许访问        跳转到Login
      页面                             页面
```

## 核心代码解析

### 1. Vuex Store 配置

```javascript
// src/store/index.js
import Vuex from "vuex";
import Vue from "vue";
import counter from "./counter";
import loginUser from "./loginUser";

Vue.use(Vuex);

const store = new Vuex.Store({
  modules: {
    counter,
    loginUser,  // 注册用户认证模块
  },
  strict: true, // 开启严格模式，只允许通过mutation改变状态
});

export default store;
```

**配置说明：**
- `modules`: 使用模块化组织Vuex代码，便于维护
- `strict: true`: 开启严格模式，确保状态只能通过mutation修改，便于调试

### 2. 用户认证模块（loginUser）

```javascript
// src/store/loginUser.js
import * as userApi from "../api/user";

export default {
  // 开启命名空间，避免命名冲突
  namespaced: true,
  
  state: {
    loading: false,  // 加载状态标识
    user: null,      // 用户信息
  },
  
  getters: {
    /**
     * 计算用户登录状态
     * @param {Object} state - 模块state
     * @returns {String} - 'loading'|'login'|'unlogin'
     */
    status(state) {
      if (state.loading) {
        return "loading";
      } else if (state.user) {
        return "login";
      } else {
        return "unlogin";
      }
    },
  },
  
  mutations: {
    setLoading(state, payload) {
      state.loading = payload;
    },
    setUser(state, payload) {
      state.user = payload;
    },
  },
  
  actions: {
    /**
     * 用户登录
     * @param {Object} ctx - Vuex context对象
     * @param {Object} payload - 登录参数 {loginId, loginPwd}
     */
    async login(ctx, payload) {
      ctx.commit("setLoading", true);
      const resp = await userApi.login(payload.loginId, payload.loginPwd);
      ctx.commit("setUser", resp);
      ctx.commit("setLoading", false);
      return resp;
    },
    
    /**
     * 获取当前用户信息（用于页面刷新时恢复登录状态）
     */
    async whoAmI(ctx) {
      ctx.commit("setLoading", true);
      const resp = await userApi.whoAmI();
      ctx.commit("setUser", resp);
      ctx.commit("setLoading", false);
    },
    
    /**
     * 用户登出
     */
    async loginOut(ctx) {
      ctx.commit("setLoading", true);
      await userApi.loginOut();
      ctx.commit("setUser", null);
      ctx.commit("setLoading", false);
    },
  },
};
```

**模块特点：**
- **命名空间**: 使用`namespaced: true`避免不同模块间的命名冲突
- **状态设计**: 使用`loading`状态管理异步操作，提升用户体验
- **Getter计算**: 通过`status` getter统一管理登录状态，便于路由守卫使用
- **异步处理**: 所有异步操作都封装在actions中，遵循Vuex最佳实践

### 3. 路由配置与导航守卫

```javascript
// src/router/routes.js
export default [
  {
    path: "/",
    component: Home,
  },
  { path: "/loading", component: Loading },
  {
    path: "/news",
    component: News,
    meta: {
      auth: true,  // 标记该路由需要登录认证
    },
  },
  { path: "/login", component: Login },
  {
    path: "/user",
    component: User,
    meta: {
      auth: true,  // 标记该路由需要登录认证
    },
  },
];
```

```javascript
// src/router/index.js
import VueRouter from "vue-router";
import routes from "./routes";
import Vue from "vue";
import store from "../store";

Vue.use(VueRouter);

const router = new VueRouter({
  routes,
  mode: "history",
});

// 全局前置守卫
router.beforeEach((to, from, next) => {
  // 参数说明：
  // to: 即将进入的路由对象
  // from: 即将离开的路由对象
  // next: 确认导航的函数
  
  if (to.meta.auth) {
    // 需要鉴权的路由
    const status = store.getters["loginUser/status"];
    
    if (status === "loading") {
      // 加载中，跳转到等待页面
      next({
        path: "/loading",
        query: {
          returnurl: to.fullPath,  // 保存目标路径，登录后跳转回来
        },
      });
    } else if (status === "login") {
      // 已登录，允许访问
      next();
    } else {
      // 未登录，跳转到登录页
      alert("该页面需要登录，你还没有登录，请先登录");
      next({
        path: "/login",
        query: {
          returnurl: to.fullPath,  // 保存目标路径，登录后跳转回来
        },
      });
    }
  } else {
    // 不需要鉴权的路由，直接放行
    next();
  }
});

export default router;
```

**路由守卫说明：**
- **meta字段**: 通过`meta.auth`标记需要鉴权的路由
- **状态判断**: 使用getter计算的`status`来判断用户登录状态
- **跳转保存**: 使用`query.returnurl`保存原始目标路径，实现登录后跳转回原页面
- **用户体验**: 在加载中状态时跳转到专门的Loading页面

### 4. 登录组件实现

```vue
<!-- src/views/Login.vue -->
<template>
  <form @submit.prevent="handleSubmit">
    <div class="form-item">
      <label>账号：</label>
      <input type="text" v-model="loginId" />
    </div>
    <div class="form-item">
      <label>密码：</label>
      <input type="password" autocomplete="new-password" v-model="loginPwd" />
    </div>
    <div class="form-item">
      <label></label>
      <button :disabled="loading">
        {{ loading ? "loading..." : "登录" }}
      </button>
    </div>
  </form>
</template>

<script>
import { mapState } from "vuex";

export default {
  data() {
    return {
      loginId: "",
      loginPwd: "",
    };
  },
  // 使用mapState辅助函数映射命名空间模块的state
  computed: mapState("loginUser", ["loading"]),
  
  methods: {
    async handleSubmit() {
      // 调用Vuex action进行登录
      const resp = await this.$store.dispatch("loginUser/login", {
        loginId: this.loginId,
        loginPwd: this.loginPwd,
      });
      
      if (resp) {
        // 登录成功，跳转到目标页面或首页
        const path = this.$route.query.returnurl || "/";
        this.$router.push(path);
      } else {
        // 登录失败，提示错误信息
        alert("账号密码错误");
      }
    },
  },
};
</script>

<style scoped>
.form-item {
  margin: 1em auto;
  width: 300px;
  display: flex;
  align-items: center;
}
.form-item input {
  height: 26px;
  font-size: 14px;
  flex: 1 1 auto;
}
.form-item label {
  width: 70px;
}
.form-item button {
  flex: 1 1 auto;
  background: #50936c;
  border: none;
  outline: none;
  color: #fff;
  border-radius: 5px;
  height: 35px;
  font-size: 16px;
}
</style>
```

**组件特点：**
- **mapState**: 使用辅助函数简化state映射，指定命名空间`"loginUser"`
- **表单处理**: 使用`@submit.prevent`阻止默认提交，手动处理登录逻辑
- **状态管理**: 按钮禁用状态与loading状态绑定，提升用户体验
- **路由跳转**: 登录成功后根据`returnurl`跳转到原页面或首页

### 5. Loading页面实现

```vue
<!-- src/views/Loading.vue -->
<template>
  <h1>正在登录中...</h1>
</template>

<script>
export default {
  created() {
    // 使用$watch监听登录状态变化
    this.unWatch = this.$store.watch(
      () => this.$store.getters["loginUser/status"],
      (status) => {
        // 当状态不再是loading时，跳转到目标页面
        if (status !== "loading") {
          this.$router
            .push(this.$route.query.returnurl || "/home")
            .catch(() => {});
        }
      },
      {
        immediate: true,  // 立即执行回调
      }
    );
  },
  
  destroyed() {
    // 组件销毁时取消监听，避免内存泄漏
    this.unWatch();
  },
};
</script>
```

**$watch使用说明：**
- **监听变化**: 监听getter计算的登录状态，当状态变化时执行回调
- **immediate选项**: 设置为`true`时，监听器创建时立即执行一次回调
- **取消监听**: 组件销毁时调用返回的取消函数，避免内存泄漏

### 6. API模块设计

```javascript
// src/api/user.js

// 模拟网络延迟
function delay(duration) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, duration);
  });
}

/**
 * 用户登录
 * @param {String} loginId - 账号
 * @param {String} loginPwd - 密码
 * @returns {Promise<Object|null>} - 登录成功返回用户信息，失败返回null
 */
export async function login(loginId, loginPwd) {
  await delay(1000);
  if (loginId === "admin" && loginPwd === "123123") {
    const user = {
      loginId,
      name: "管理员",
    };
    localStorage.setItem("user", JSON.stringify(user));
    return user;
  }
  return null;
}

/**
 * 用户登出
 */
export async function loginOut() {
  await delay(1000);
  localStorage.removeItem("user");
}

/**
 * 获取当前登录用户信息
 * @returns {Promise<Object|null>} - 返回用户信息或null
 */
export async function whoAmI() {
  await delay(1000);
  const user = localStorage.getItem("user");
  if (user) {
    return JSON.parse(user);
  }
  return null;
}
```

**API设计说明：**
- **延迟模拟**: 使用`delay`函数模拟网络请求，展示异步处理过程
- **本地存储**: 使用localStorage保存用户信息，实现持久化登录
- **错误处理**: 登录失败返回null，由调用方处理错误逻辑

### 7. 应用初始化

```javascript
// src/main.js
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";

// 应用启动时检查登录状态
store.dispatch("loginUser/whoAmI");

new Vue({
  router,
  store,
  render: (h) => h(App),
}).$mount("#app");
```

**初始化流程：**
1. 应用启动时调用`whoAmI` action
2. 从localStorage读取用户信息
3. 根据读取结果设置Vuex状态
4. 路由守卫根据状态决定用户可以访问的页面

## 核心知识点总结

### 1. Vuex模块化（Modules）

```javascript
// 模块定义
const moduleA = {
  namespaced: true,  // 开启命名空间
  state: { /* ... */ },
  getters: { /* ... */ },
  mutations: { /* ... */ },
  actions: { /* ... */ }
};

// Store中注册
const store = new Vuex.Store({
  modules: {
    a: moduleA
  }
});
```

**访问模块中的内容：**
```javascript
// 在组件中访问
this.$store.state.a.xxx        // 访问state
this.$store.getters['a/yyy']   // 访问getters
this.$store.dispatch('a/zzz')   // 访问actions
```

### 2. 辅助函数与命名空间

```javascript
// 在组件中使用mapState映射命名空间模块
import { mapState, mapGetters, mapActions } from 'vuex';

export default {
  computed: {
    // 映射命名空间模块的state
    ...mapState('loginUser', ['loading', 'user']),
    
    // 映射命名空间模块的getters
    ...mapGetters('loginUser', ['status'])
  },
  methods: {
    // 映射命名空间模块的actions
    ...mapActions('loginUser', ['login', 'loginOut'])
  }
}
```

### 3. 路由导航守卫

```javascript
// 全局前置守卫
router.beforeEach((to, from, next) => {
  // to: 即将进入的目标路由对象
  // from: 即将离开的起始路由对象
  // next: 确认导航的函数
  
  // 必须调用next()，否则导航会被阻止
  next();           // 放行
  next(false);      // 取消导航
  next('/path');    // 跳转到其他路由
  next({ path: '/path', query: { key: 'value' } });  // 带参数跳转
});
```

### 4. $watch的使用

```javascript
// 在组件中监听Vuex状态变化
this.unWatch = this.$store.watch(
  // getter函数，返回要监听的值
  (state, getters) => getters['xxx/yyy'],
  
  // 回调函数，当值变化时执行
  (newValue, oldValue) => {
    // 处理逻辑
  },
  
  // 选项对象
  {
    immediate: true,  // 立即执行回调
    deep: true        // 深度监听
  }
);

// 取消监听
this.unWatch();
```

### 5. 状态设计最佳实践

```javascript
// 良好的状态设计
state: {
  loading: false,    // 异步操作状态
  data: null,        // 业务数据
  error: null        // 错误信息
}

// 通过getter计算衍生状态
getters: {
  status(state) {
    if (state.loading) return 'loading';
    if (state.error) return 'error';
    if (state.data) return 'success';
    return 'idle';
  }
}
```

## 项目文件结构

```
user-demo/
├── src/
│   ├── api/
│   │   └── user.js              # 用户API接口
│   ├── router/
│   │   ├── index.js             # 路由配置
│   │   └── routes.js            # 路由定义
│   ├── store/
│   │   ├── index.js             # Vuex主文件
│   │   ├── counter.js           # 计数器模块
│   │   └── loginUser.js         # 用户认证模块
│   ├── views/
│   │   ├── Home.vue             # 首页
│   │   ├── Login.vue            # 登录页
│   │   ├── Loading.vue          # 加载页
│   │   ├── User.vue             # 个人中心
│   │   └── News.vue             # 新闻页
│   ├── App.vue
│   └── main.js                  # 应用入口
```

## 参考资料

### Vue.js官方文档
- [watch配置](https://cn.vuejs.org/v2/api/#watch)
- [Vue.prototype.$watch](https://cn.vuejs.org/v2/api/#vm-watch)

### Vuex官方文档
- [mapState辅助函数](https://vuex.vuejs.org/zh/guide/state.html#mapstate-%E8%BE%85%E5%8A%A9%E5%87%BD%E6%95%B0)
- [getters](https://vuex.vuejs.org/zh/guide/getters.html)
- [mapGetters辅助函数](https://vuex.vuejs.org/zh/guide/getters.html#mapgetters-%E8%BE%85%E5%8A%A9%E5%87%BD%E6%95%B0)
- [modules](https://vuex.vuejs.org/zh/guide/modules.html)
- [watch](https://vuex.vuejs.org/zh/api/#watch)

### Vue Router官方文档
- [exact-path](https://router.vuejs.org/api/#exact-path)
- [导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E5%85%A8%E5%B1%80%E5%89%8D%E7%BD%AE%E5%AE%88%E5%8D%AB)

## 练习建议

1. **基础练习**：
   - 添加用户注册功能
   - 实现记住密码功能
   - 添加登录失败次数限制

2. **进阶练习**：
   - 添加用户权限管理
   - 实现多角色登录系统
   - 添加登录状态过期处理

3. **挑战练习**：
   - 实现JWT令牌认证
   - 添加刷新令牌机制
   - 实现单点登录（SSO）

## 测试账号

- 账号：`admin`
- 密码：`123123`

## 注意事项

1. **状态持久化**：当前使用localStorage，实际项目应考虑安全性和token管理
2. **路由模式**：使用history模式需要服务器配置支持
3. **严格模式**：生产环境应关闭严格模式以提升性能
4. **错误处理**：应完善API调用的错误处理逻辑
5. **安全性**：实际项目需考虑CSRF、XSS等安全问题
