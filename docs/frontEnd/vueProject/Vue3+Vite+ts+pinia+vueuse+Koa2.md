# Vue3 + Vite + ts + pinia + vueuse + Koa2

## 项目初始化

### 按提示初始化

1. vite-cli

   ```shell
   # pnpm
   pnpm create vite
   
   # npm
   npm init vite@latest
   
   # yarn
   yarn create vite
   ```

2. 输入项目名

   ```shell
   ? Project name:  vite-vue3-ts-pinia
   ```

3. 选择一个框架

   ```shell
   ? Select a framework: » - Use arrow-keys. Return to submit.
        vanilla // 原生js
    >   vue     // 默认就是 vue3
        react   // react
        preact  // 轻量化react框架
        lit     // 轻量级web组件
        svelte  // svelte框架
   ```

4. 使用 typescript

   ```shell
   ? Select a variant: › - Use arrow-keys. Return to submit.
        vue
    ❯   vue-ts
   ```

5. 启动项目

   ```shell
   cd vite-vue3-ts-pinia && pnpm install && pnpm run dev
   ```

### 快速初始化

```shell
# pnpm
pnpm create vite project-name -- --template vue-ts

# npm 6.x
npm init vite@latest project-name --template vue-ts
 
# npm 7+, 需要额外的双横线：
npm init vite@latest project-name -- --template vue-ts
 
# yarn
yarn create vite project-name --template vue-ts
```

### 集成配置

1. 保证 node 的使用

   ```shell
   pnpm i @types/node --save-dev
   ```

2. 修改 `tsconfig.json`

   ```json
   {
     "compilerOptions": {
       "typeRoots": [
         "node_modules/@types", // 默认值
         "src/types"
      ],
       "target": "esnext",
       "useDefineForClassFields": true,
       "module": "esnext",
       "moduleResolution": "node",
       "strict": true,
       "jsx": "preserve",
       "sourceMap": true,
       "resolveJsonModule": true,
       "esModuleInterop": true,
       "lib": ["esnext", "dom"],
       "baseUrl": "./",
       "paths":{
         "@": ["src"],
         "@/*": ["src/*"],
       }
     },
     "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"]
   }
   ```

3. 修改 `vite.config.ts`

   ```typescript
   import { defineConfig } from 'vite';
   import vue from '@vitejs/plugin-vue';
   import * as path from 'path';
   
   // https://vitejs.dev/config/
   export default defineConfig({
       resolve: {
           //设置别名
           alias: {
               '@': path.resolve(__dirname, 'src')
           }
       },
       plugins: [vue()],
       server: {
           port: 8080, //启动端口
           hmr: {
               host: '127.0.0.1',
               port: 8080
           },
           // 设置 https 代理
           proxy: {
               '/api': {
                   target: 'your https address',
                   changeOrigin: true,
                   rewrite: (path: string) => path.replace(/^\/api/, '')
               }
           }
       }
   });
   ```

## 代码质量风格统一

### 集成 eslint

1. 安装

   ```shell
   pnpm i eslint eslint-plugin-vue --save-dev
   ```

   由于 ESLint 默认使用 [Espree](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Feslint%2Fespree) 进行语法解析，无法识别 TypeScript 的一些语法，所以需要安装 [`@typescript-eslint/parser`](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftypescript-eslint%2Ftypescript-eslint%2Ftree%2Fmaster%2Fpackages%2Fparser) 替代掉默认的解析器

   ```shell
   pnpm install @typescript-eslint/parser --save-dev
   ```

   安装对应的插件 [@typescript-eslint/eslint-plugin](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftypescript-eslint%2Ftypescript-eslint%2Ftree%2Fmaster%2Fpackages%2Feslint-plugin) 它作为 eslint 默认规则的补充，提供了一些额外的适用于 ts 语法的规则。

   ```shell
   pnpm install @typescript-eslint/eslint-plugin --save-dev
   ```

2. 创建配置文件： `.eslintrc.js` 或 `.eslintrc.json`

   ```javascript
   module.exports = {
       parser: 'vue-eslint-parser',
   
       parserOptions: {
           parser: '@typescript-eslint/parser',
           ecmaVersion: 2020,
           sourceType: 'module',
           ecmaFeatures: {
               jsx: true
           }
       },
   
       extends: [
           'plugin:vue/vue3-recommended',
           'plugin:@typescript-eslint/recommended',
       ],
   
       rules: {
           // override/add rules settings here, such as:
       }
   };
   ```

3. 创建忽略文件：`.eslintignore`

   ```
   node_modules/
   dist/
   index.html
   ```

4. 命令行式运行：修改 `package.json`

   ```json
   {
       ...
       "scripts": {
           ...
           "eslint:comment": "使用 ESLint 检查并自动修复 src 目录下所有扩展名为 .js 和 .vue 的文件",
           "eslint": "eslint --ext .js,.vue --ignore-path .gitignore --fix src",
       }
       ...
   }
   ```

## 集成 prettier

1. 安装

   ```shell
   pnpm i prettier eslint-config-prettier eslint-plugin-prettier --save-dev
   ```

2. 创建配置文件： `prettier.config.js` 或 `.prettierrc.js`

   ```javascript
   module.exports = {
       // 一行最多 80 字符
       printWidth: 80,
       // 使用 4 个空格缩进
       tabWidth: 4,
       // 不使用 tab 缩进，而使用空格
       useTabs: false,
       // 行尾需要有分号
       semi: true,
       // 使用单引号代替双引号
       singleQuote: true,
       // 对象的 key 仅在必要时用引号
       quoteProps: 'as-needed',
       // jsx 不使用单引号，而使用双引号
       jsxSingleQuote: false,
       // 末尾使用逗号
       trailingComma: 'all',
       // 大括号内的首尾需要空格 { foo: bar }
       bracketSpacing: true,
       // jsx 标签的反尖括号需要换行
       jsxBracketSameLine: false,
       // 箭头函数，只有一个参数的时候，也需要括号
       arrowParens: 'always',
       // 每个文件格式化的范围是文件的全部内容
       rangeStart: 0,
       rangeEnd: Infinity,
       // 不需要写文件开头的 @prettier
       requirePragma: false,
       // 不需要自动在文件开头插入 @prettier
       insertPragma: false,
       // 使用默认的折行标准
       proseWrap: 'preserve',
       // 根据显示样式决定 html 要不要折行
       htmlWhitespaceSensitivity: 'css',
       // 换行符使用 lf
       endOfLine: 'auto'
   }
   ```

3. 修改 `.eslintrc.js` 配置

   ```javascript
   module.exports = {
       ...
   
       extends: [
           'plugin:vue/vue3-recommended',
           'plugin:@typescript-eslint/recommended',
           'prettier',
           'plugin:prettier/recommended'
       ],
   
       ...
   };
   ```

4. 命令行式运行：修改 `package.json`

   ```json
   {
       ...
       "scripts": {
           ...
           "prettier:comment": "自动格式化当前目录下的所有文件",
           "prettier": "prettier --write"
       }
       ...
   }
   ```

## 集成 pinia

### 安装

```shell
 pnpm i pinia --save
```

### 使用

1. 新建 src/store 目录并在其下面创建 index.ts，导出 store

   ```typescript
    import { createPinia } from 'pinia'
   
    const store = createPinia()
   
    export default store
   ```

2. 在 main.ts 中引入并使用

   ```typescript
    import { createApp } from 'vue'
    import App from './App.vue'
    import store from './store'
    
    // 创建vue实例
    const app = createApp(App)
    
    // 挂载pinia
    app.use(store)
    
    // 挂载实例
    app.mount('#app');
   ```

3. **定义State：** 在 src/store 下面创建一个 user.ts

   ```typescript
    import { defineStore } from 'pinia'
   
    export const useUserStore = defineStore({
      id: 'user', // id必填，且需要唯一
      state: () => {
        return {
          name: '张三'
        }
      },
      actions: {
        updateName(name) {
          this.name = name
        }
      }
    })
   ```

4. **获取State：** 在 src/components/usePinia.vue 中使用

   ```vue
    <template>
      <div>{{ userStore.name }}</div>
    </template>
   
    <script lang="ts" setup>
    import { useUserStore } from '@/store/user'
   
    const userStore = useUserStore()
    </script>
   ```

5. **修改State：**

   ```vue
    // 1. 直接修改 state （不建议）
    userStore.name = '李四'
   
    // 2. 通过 actions 去修改
    <script lang="ts" setup>
    import { useUserStore } from '@/store/user'
   
    const userStore = useUserStore()
    userStore.updateName('李四')
    </script>
   ```

## 集成 vue-router4

### 安装

```shell
 pnpm i vue-router --save
```

### 使用

1. 新建 src/router 目录并在其下面创建 index.ts，导出 router

   ```typescript
    import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router';
   
    const routes: Array<RouteRecordRaw> = [
      {
        path: '/login',
        name: 'Login',
        meta: {
            title: '登录',
            keepAlive: true,
            requireAuth: false
        },
        component: () => import('@/pages/login.vue')
      },
      {
          path: '/',
          name: 'Index',
          meta: {
              title: '首页',
              keepAlive: true,
              requireAuth: true
          },
          component: () => import('@/pages/index.vue')
      }
    ]
   
    const router = createRouter({
      history: createWebHistory(),
      routes
    });
    export default router;
   ```

2. 在 main.ts 中引入并使用

   ```typescript
    import { createApp } from 'vue'
    import App from './App.vue'
    import store from './store'
    import router from '@/router';
   
    // 创建vue实例
    const app = createApp(App);
   
    app.use(router);
   
    // 挂载实例
    app.mount('#app');
   ```

3. 修改 App.vue

   ```vue
    <template>
      <RouterView/>
    </template>
   ```

## 集成 vueuse

### 安装

```shell
 pnpm i @vueuse/core
```

### 使用

创建一个新的 src/page/vueUse.vue 页面来做一个简单的 demo

```vue
 <template>
   <h1> 测试 vueUse 的鼠标坐标 </h1>
   <h3>Mouse: {{x}} x {{y}}</h3>
 </template>

 <script lang="ts">
     import { defineComponent } from 'vue';
     import { useMouse } from '@vueuse/core'

     export default defineComponent({
         name: 'VueUse',
         setup() {
           const { x, y } = useMouse()

           return {
             x, y
           }
         }
     });
 </script>
```

## Scss 或 Less

```shell
 # .scss and .sass
 pnpm add -D sass

 # .less
 pnpm add -D less
```

## 集成 Axios

### 安装

```shell
 pnpm i axios
```

### 使用

1. 新建 src/utils/axios.ts

   ```typescript
    import axios, { AxiosResponse, AxiosRequestConfig } from 'axios';
   
    const service = axios.create();
   
    // Request interceptors
    service.interceptors.request.use(
        (config: AxiosRequestConfig) => {
            // do something
            return config;
        },
        (error: any) => {
            Promise.reject(error);
        }
    );
   
    // Response interceptors
    service.interceptors.response.use(
        async (response: AxiosResponse) => {
            // do something
        },
        (error: any) => {
            // do something
            return Promise.reject(error);
        }
    );
   
    export default service;
   ```

2. 在页面中使用即可

   ```vue
   <script lang="ts">
       import request from '@/utils/axios';
       const requestRes = async () => {
         let result = await request({
           url: '/api/xxx',
           method: 'get'
         });
       }
   </script>
   ```

## 封装请求参数和响应数据的所有 api

1. 新建 `src/api/index.ts`

   ```typescript
   import * as login from './module/login';
   import * as index from './module/index';
   
   export default Object.assign({}, login, index);
   ```

2. 新建 `src/api/module/login.ts` 和 `src/api/module/index.ts`

   ```typescript
   import request from '@/utils/axios';
   
   /**
    * 登录
    */
    
   interface IResponseType<P = {}> {
       code?: number;
       status: number;
       msg: string;
       data: P;
   }
   interface ILogin {
       token: string;
       expires: number;
   }
   export const login = (username: string, password: string) => {
       return request<IResponseType<ILogin>>({
           url: '/api/auth/login',
           method: 'post',
           data: {
               username,
               password
           }
       });
   };
   ```

3. 由于使用了 typescript，所以需新增 `src/types/shims-axios.d.ts`

   ```typescript
   import { AxiosRequestConfig } from 'axios';
   /**
    * 自定义扩展axios模块
    * @author Maybe
    */
   declare module 'axios' {
       export interface AxiosInstance {
           <T = any>(config: AxiosRequestConfig): Promise<T>;
           request<T = any>(config: AxiosRequestConfig): Promise<T>;
           get<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>;
           delete<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>;
           head<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>;
           post<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>;
           put<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>;
           patch<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>;
       }
   }
   ```

4. 在 `src/pages/request.vue` 页面中使用

   ```vue
   <script lang="ts">
     import API from '@/api';
   
     const requestRes = async () => {
       let result = await API.login('zhangsan', '123456');
     }
   </script>
   ```

# 使用 [commitizen](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcommitizen%2Fcz-cli) 规范git提交

## 安装 commitizen

1. 安装

   ```shell
   pnpm install -D commitizen cz-conventional-changelog @commitlint/config-conventional @commitlint/cli commitlint-config-cz cz-customizable
   ```

2. 配置 `package.json`

   ```json
   {
     ...
     "scripts": {
       "commit:comment": "引导设置规范化的提交信息",
       "commit":"git-cz",
     },
   
     "config": {
         "commitizen": {
           "path": "node_modules/cz-customizable"
         }
     },
     ...
   }
   ```

3. 新增配置 `commitlint.config.js`

   ```javascript
   module.exports = {
       extends: ['@commitlint/config-conventional', 'cz'],
       rules: {
           'type-enum': [
               2,
               'always',
               [
                   'feature', // 新功能（feature）
                   'bug', // 此项特别针对bug号，用于向测试反馈bug列表的bug修改情况
                   'fix', // 修补bug
                   'ui', // 更新 ui
                   'docs', // 文档（documentation）
                   'style', // 格式（不影响代码运行的变动）
                   'perf', // 性能优化
                   'release', // 发布
                   'deploy', // 部署
                   'refactor', // 重构（即不是新增功能，也不是修改bug的代码变动）
                   'test', // 增加测试
                   'chore', // 构建过程或辅助工具的变动
                   'revert', // feat(pencil): add ‘graphiteWidth’ option (撤销之前的commit)
                   'merge', // 合并分支， 例如： merge（前端页面）： feature-xxxx修改线程地址
                   'build', // 打包
               ],
           ],
           // <type> 格式 小写
           'type-case': [2, 'always', 'lower-case'],
           // <type> 不能为空
           'type-empty': [2, 'never'],
           // <scope> 范围不能为空
           'scope-empty': [2, 'never'],
           // <scope> 范围格式
           'scope-case': [0],
           // <subject> 主要 message 不能为空
           'subject-empty': [2, 'never'],
           // <subject> 以什么为结束标志，禁用
           'subject-full-stop': [0, 'never'],
           // <subject> 格式，禁用
           'subject-case': [0, 'never'],
           // <body> 以空行开头
           'body-leading-blank': [1, 'always'],
           'header-max-length': [0, 'always', 72],
       },
   };
   ```

4. 自定义提示则添加 `.cz-config.js`

   ```javascript
   module.exports = {
       types: [
           {value: 'feature',  name: 'feature:  增加新功能'},
           {value: 'bug',      name: 'bug:      测试反馈bug列表中的bug号'},
           {value: 'fix',      name: 'fix:      修复bug'},
           {value: 'ui',       name: 'ui:       更新UI'},
           {value: 'docs',     name: 'docs:     文档变更'},
           {value: 'style',    name: 'style:    代码格式(不影响代码运行的变动)'},
           {value: 'perf',     name: 'perf:     性能优化'},
           {value: 'refactor', name: 'refactor: 重构(既不是增加feature，也不是修复bug)'},
   	{value: 'release',  name: 'release:  发布'},
   	{value: 'deploy',   name: 'deploy:   部署'},
           {value: 'test',     name: 'test:     增加测试'},
           {value: 'chore',    name: 'chore:    构建过程或辅助工具的变动(更改配置文件)'},
           {value: 'revert',   name: 'revert:   回退'},
       	{value: 'build',    name: 'build:    打包'}
       ],
       // override the messages, defaults are as follows
       messages: {
           type: '请选择提交类型:',
           customScope: '请输入您修改的范围(可选):',
           subject: '请简要描述提交 message (必填):',
           body: '请输入详细描述(可选，待优化去除，跳过即可):',
           footer: '请输入要关闭的issue(待优化去除，跳过即可):',
           confirmCommit: '确认使用以上信息提交？(y/n/e/h)'
       },
       allowCustomScopes: true,
       skipQuestions: ['body', 'footer'],
       subjectLimit: 72
   };
   ```

5. 交互界面测试

   ![carbon.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2be96b13d3c427e919b11e5bc5404e4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 安装 husky

1. 安装

   ```shell
   # 1.安装
   pnpm i husky lint-staged -D
   
   # 2.生成 .husky 的文件夹
   npx husky install
   
   # 3.添加 hooks，会在 .husky 目录下生成一个 pre-commit 脚本文件
   npx husky add .husky/pre-commit "npx --no-install lint-staged"
   
   # 4.添加 commit-msg
   npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
   
   # 5. 使用 `git commit -m "message"` 就会看到 hook 生效了。
   ```

2. 添加配置 `package.json`

   ```json
   {
     ...
     "lint-staged": {
       	"*.{js,ts}": [
               "npm run eslint",
               "npm run prettier"
       	]
     }
     ...
   }
   ```

## 提交日志

[standard-version](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fconventional-changelog%2Fstandard-version) 或者 [conventional-changelog](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fconventional-changelog%2Fconventional-changelog)

## 修改客户端入口

1. 修改 `~/src/main.ts`

   ```typescript
   import { createSSRApp } from "vue";
   import App from "./App.vue";
   
   // 为了保证数据的互不干扰，每次请求需要导出一个新的实例
   export const createApp = () => {
       const app = createSSRApp(App);
       return { app };
   }
   ```

2. 新建 `~/src/entry-client.ts`

   ```typescript
   import { createApp } from "./main"
   
   const { app } = createApp();
   
   app.mount("#app");
   ```

3. 修改 `~/index.html` 的入口

   ```html
   <!DOCTYPE html>
   <html lang="en">
       ...
       <script type="module" src="/src/entry-client.ts"></script>
       ...
   </html>
   ```

## 创建开发服务器

### 使用 Koa2

1. 安装 `koa2`

   ```shell
   pnpm i koa --save && pnpm i @types/koa --save-dev
   ```

2. 安装中间件 `koa-connect`

   ```shell
   pnpm i koa-connect --save
   ```

3. 使用：新建 `~/server.js`

   ```typescript
   const Koa = require('koa');
   
   (async () => {
       const app = new Koa();
   
       app.use(async (ctx) => {
           ctx.body = `<!DOCTYPE html>
         <html lang="en">
           <head><title>koa2 + vite + ts + vue3 + vue-router</title></head>
           <body>
             <h1 style="text-align: center;">使用 koa2 + vite + ts + vue3 + vue-router 集成前端 SSR 企业级项目</h1>
           </body>
         </html>`;
       });
   
       app.listen(9000, () => {
           console.log('server is listening in 9000');
       });
   })();
   ```

4. 运行 `node server.js`

## 渲染替换成项目根目录下的 index.html

1. 修改 `server.js` 中的 `ctx.body` 返回的是 `index.html`

   ```javascript
    const fs = require('fs');
    const path = require('path');
    
    const Koa = require('koa');
    
    (async () => {
        const app = new Koa();
    
        // 获取 index.html
        const template = fs.readFileSync(path.resolve(__dirname, 'index.html'), 'utf-8');
    
        app.use(async (ctx) => {
            ctx.body = template;
        });
    
        app.listen(9000, () => {
            console.log('server is listening in 9000');
        });
    })();
   ```

2. 运行 `node server.js`后， 我们就会看到返回的是空白内容的 `index.html` 了，但是我们需要返回的是 vue 模板 ，那么我们只需要做个正则的替换

3. 给 `index.html` 添加 `<!--app-html-->` 标记

   ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <link rel="icon" href="/favicon.ico" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>koa2 + vite + ts + vue3</title>
      </head>
      <body>
        <div id="app"><!--app-html--></div>
        <script type="module" src="/src/entry-client.ts"></script>
      </body>
    </html>
   ```

4. 修改 `server.js` 中的 `ctx.body`

   ```javascript
   // other code ...
   
   (async () => {
       const app = new Koa();
   
       // 获取index.html
       const template = fs.readFileSync(path.resolve(__dirname, 'index.html'), 'utf-8');
   
       app.use(async (ctx) => {
           let vueTemplate = '<h1 style="text-align:center;">现在假装这是一个vue模板</h1>';
   
           // 替换 index.html 中的 <!--app-html--> 标记
           let html = template.replace('<!--app-html-->', vueTemplate);
   
           ctx.body = html;
       });
   
       app.listen(9000, () => {
           console.log('server is listening in 9000');
       });
   })();
   ```

5. 运行 `node server.js`后，我们就会看到返回的变量 vueTemplate 内容

# 新增服务端入口

因为 vue 返回的是 `vue 实例模板` 而不是 `可渲染的 html` ，所以我们需要使用 `@vue/server-renderer` 进行转换

1. 安装 `@vue/server-renderer`

   ```shell
   pnpm i @vue/server-renderer --save
   ```

2. 新建 `~/src/entry-server.ts`

   ```typescript
   import { createApp } from './main';
   import { renderToString } from '@vue/server-renderer';
   
   export const render = async () => {
     const { app } = createApp();
   	
     // 注入vue ssr中的上下文对象
     const renderCtx: {modules?: string[]} = {}
   
     let renderedHtml = await renderToString(app, renderCtx)
   
     return { renderedHtml };
   }
   ```

# 注入 vite

修改 `~/server.js`

```javascript
const fs = require('fs')
const path = require('path')

const Koa = require('koa')
const koaConnect = require('koa-connect')

const vite = require('vite')

;(async () => {
    const app = new Koa();

    // 创建 vite 服务
    const viteServer = await vite.createServer({
        root: process.cwd(),
        logLevel: 'error',
        server: {
        middlewareMode: true,
        },
    })
    
    // 注册 vite 的 Connect 实例作为中间件（注意：vite.middlewares 是一个 Connect 实例）
    app.use(koaConnect(viteServer.middlewares))

    app.use(async ctx => {
        try {
            // 1. 获取index.html
            let template = fs.readFileSync(path.resolve(__dirname, 'index.html'), 'utf-8');

            // 2. 应用 Vite HTML 转换。这将会注入 Vite HMR 客户端，
            template = await viteServer.transformIndexHtml(ctx.path, template)

            // 3. 加载服务器入口, vite.ssrLoadModule 将自动转换
            const { render } = await viteServer.ssrLoadModule('/src/entry-server.ts')

            //  4. 渲染应用的 HTML
            const { renderedHtml } = await render(ctx, {})

            const html = template.replace('<!--app-html-->', renderedHtml)

            ctx.type = 'text/html'
            ctx.body = html
        } catch (e) {
            viteServer && viteServer.ssrFixStacktrace(e)
            console.log(e.stack)
            ctx.throw(500, e.stack)
        }
    })

    app.listen(9000, () => {
        console.log('server is listening in 9000');
    });

})()
```

# 添加开发环境

为了将 SSR 项目可以在生产环境运行，我们需要：

1. 正常构建生成一个 `客户端构建包`；

2. 再生成一个 SSR 构建，使其通过 `require()` 直接加载，这样便无需再使用 Vite 的 `ssrLoadModule`；

3. 修改 `package.json`

   ```json
   ...
   {
   "scripts": {
       // 开发环境
       "dev": "node server-dev.js",
       // 生产环境
       "server": "node server-prod.js",
       // 构建
       "build": "pnpm build:client && pnpm build:server",
       "build:client": "vite build --outDir dist/client",
       "build:server": "vite build --ssr src/entry-server.js --outDir dist/server",
     },
   }
   ...
   ```

4. 修改 `server.js` 为 `server-dev.js`

5. 运行 `pnpm run build` 构建包

6. 新增 `server-prod.js`

   > 注意：为了处理静态资源，需要在此新增 `koa-send` 中间件： `pnpm i koa-send --save`

   ```javascript
   const Koa = require('koa');
   const sendFile = require('koa-send');
   
   const path = require('path');
   const fs = require('fs');
   
   const resolve = (p) => path.resolve(__dirname, p);
   
   const clientRoot = resolve('dist/client');
   const template = fs.readFileSync(resolve('dist/client/index.html'), 'utf-8');
   const render = require('./dist/server/entry-server.js').render;
   const manifest = require('./dist/client/ssr-manifest.json');
   
   (async () => {
     const app = new Koa();
   
     app.use(async (ctx) => {
   				
       // 请求的是静态资源
       if (ctx.path.startsWith('/assets')) {
         await sendFile(ctx, ctx.path, { root: clientRoot });
         return;
       }
   
       const [ appHtml ] = await render(ctx, manifest);
   
       const html = template.replace('<!--app-html-->', appHtml);
   
       ctx.type = 'text/html';
       ctx.body = html;
     });
   
     app.listen(8080, () => console.log('started server on http://localhost:8080'));
   })();
   ```

# 预加载

在用户获取 `服务端模板` (也就是执行 `vite build` 后生成的 `dist/client` 目录) 的时候，直接在 html 中把对应的 `js` 和 `css` 文件预渲染了，这就是 `静态站点生成（SSG）` 的形式。

1. `生成预加载指令`：在 package.json 中的 `build:client` 添加 `--ssrManifest` 标志，运行后生成 `ssr-manifest.json`

   ```json
   ...
   {
   "scripts": {
       ...
       "build:client": "vite build --ssrManifest --outDir dist/client",
       ...
     },
   }
   ...
   ```

2. `生成预加载指令`：在 package.json 中的 `build:client` 添加 `--ssrManifest` 标志，运行后生成 `ssr-manifest.json`

   ```typescript
   export const render = async (
       ctx: ParameterizedContext,
       manifest: Record<string, string[]>
   ): Promise<[string, string]> => {
       const { app } = createApp();
       console.log(ctx, manifest, '');
   
       const renderCtx: { modules?: string[] } = {};
   
       const renderedHtml = await renderToString(app, renderCtx);
   
       const preloadLinks = renderPreloadLinks(renderCtx.modules, manifest);
   
       return [renderedHtml, preloadLinks];
   };
   
   /**
    * 解析需要预加载的链接
    * @param modules
    * @param manifest
    * @returns string
    */
   function renderPreloadLinks(
       modules: undefined | string[],
       manifest: Record<string, string[]>
   ): string {
       let links = '';
       const seen = new Set();
       if (modules === undefined) throw new Error();
       modules.forEach((id) => {
           const files = manifest[id];
           if (files) {
               files.forEach((file) => {
                   if (!seen.has(file)) {
                       seen.add(file);
                       links += renderPreloadLink(file);
                   }
               });
           }
       });
       return links;
   }
   
   /**
    * 预加载的对应的地址
    * 下面的方法只针对了 js 和 css，如果需要处理其它文件，自行添加即可
    * @param file
    * @returns string
    */
   function renderPreloadLink(file: string): string {
       if (file.endsWith('.js')) {
           return `<link rel="modulepreload" crossorigin href="${file}">`;
       } else if (file.endsWith('.css')) {
           return `<link rel="stylesheet" href="${file}">`;
       } else {
           return '';
       }
   }
   ```

3. 给 `index.html` 添加 `<!--preload-links-->` 标记

4. 改造 `server-prod.js`

   ```javascript
   ...
   
   (async () => {
       const app = new Koa();
   
       app.use(async (ctx) => {
   				
   	...
   
           const [appHtml, preloadLinks] = await render(ctx, manifest);
   
           const html = template
               .replace('<!--preload-links-->', preloadLinks)
               .replace('<!--app-html-->', appHtml);
   
           // do something
       });
   
       app.listen(8080, () => console.log('started server on http://localhost:8080'));
   })();
   ```

5. 运行 `pnpm run build && pnpm run serve` 就可正常显示了

## 修改Vue-router

1. 修改 `src/router/index.ts`

   ```typescript
   import {
       createRouter as createVueRouter,
       createMemoryHistory,
       createWebHistory,
       Router
   } from 'vue-router';
   
   export const createRouter = (type: 'client' | 'server'): Router =>
       createVueRouter({
           history: type === 'client' ? createWebHistory() : createMemoryHistory(),
   
           routes: [
               {
                   path: '/',
                   name: 'index',
                   meta: {
                       title: '首页',
                       keepAlive: true,
                       requireAuth: true
                   },
                   component: () => import('@/pages/index.vue')
               },
               {
                   path: '/login',
                   name: 'login',
                   meta: {
                       title: '登录',
                       keepAlive: true,
                       requireAuth: false
                   },
                   component: () => import('@/pages/login.vue')
               },
               {
                   path: '/user',
                   name: 'user',
                   meta: {
                       title: '用户中心',
                       keepAlive: true,
                       requireAuth: true
                   },
                   component: () => import('@/pages/user.vue')
               }
           ]
       });
   ```

2. 修改入口文件 `src/enter-client.ts`

   ```typescript
   import { createApp } from './main';
   
   import { createRouter } from './router';
   const router = createRouter('client');
   
   const { app } = createApp();
   
   app.use(router);
   
   router.isReady().then(() => {
       app.mount('#app', true);
   });
   ```

3. 修改入口文件 `src/enter-server.ts`

   ```typescript
   ...
   import { createRouter } from './router'
   const router = createRouter('client');
   
   export const render = async (
       ctx: ParameterizedContext,
       manifest: Record<string, string[]>
   ): Promise<[string, string]> => {
       const { app } = createApp();
   
       // 路由注册
       const router = createRouter('server');
       app.use(router);
       await router.push(ctx.path);
       await router.isReady();
   
       ...
   };
   ...
   ```

## 修改pinia配置

1. 注入 `pinia` ：修改 `src/entry-client.ts`

   ```typescript
   ...
   
   import createStore from '@/store';
   const pinia = createStore();
   
   const { app } = createApp();
   
   app.use(router);
   app.use(pinia);
   
   // 初始化 pini
   // 注意：__INITIAL_STATE__需要在 src/types/shims-global.d.ts中定义
   if (window.__INITIAL_STATE__) {
       pinia.state.value = JSON.parse(window.__INITIAL_STATE__);
   }
   
   ...
   ```

2. 修改 `src/entry-server.ts`

   ```typescript
   ...
   
   import createStore from '@/store';
   
   export const render = () => {
       ...
       // pinia
       const pinia = createStore();
       app.use(pinia);
       const state = JSON.stringify(pinia.state.value);
   
       ...
   
       return [renderedHtml, state, preloadLinks];
   
   }
   ...
   ```

3. 修改 `server-dev.js` 和 `server-prod.js`

   ```javascript
   ...
   
   const [renderedHtml, state, preloadLinks] = await render(ctx, {});
   
   const html = template
        .replace('<!--app-html-->', renderedHtml)
        .replace('<!--pinia-state-->', state);
       // server-prod.js
       .replace('<!--preload-links-->', preloadLinks)
   
   ...
   ```

4. 给 `index.html` 添加 `<!--pinia-state-->` 标记

   ```html
   <script>
       window.__INITIAL_STATE__ = '<!--pinia-state-->';
   </script>
   ```

## 模版地址

[传送门](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fjeddygong%2Fvite-templates%2Ftree%2Fmaster%2Fkoa2-ssr-vite-vue3-ts-pinia)