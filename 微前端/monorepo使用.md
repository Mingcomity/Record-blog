# monorepo

## 安装及使用

1. 初始化`pnpm init`

2. 新建文件 `pnpm-workspace.yaml`

   ```yaml
   packages:
     # 我负责的所有的前端项目存储的地方
     - 'packages/*'
     # 公共的，给我所有的项目使用的公开组件
     - 'components/**'
     # 公共的方法 api -axios
     - 'api/**'
   ```

3. 新建文件夹 `packages`  `api`

4. 处理 `api` 文件夹，对axios的封装

   - 终端执行 `cd api`

   - 终端执行 `pnpm init`

   - 终端执行 `pnpm add axios`

   - 对axios进行封装，导出

   - 修改 `api/package.json`

     - ```json
       {
         "name": "@wl/api",
         "version": "1.0.1",
         "description": "",
         "main": "index.js",
         "private": true,
         "scripts": {
           "test": "echo \"Error: no test specified\" && exit 1"
         },
         "keywords": [],
         "author": "",
         "license": "ISC",
         "dependencies": {
           "axios": "^1.3.3"
         }
       }
       
       ```

5. `packages`下新建 `web` Vue 项目

   - `pnpm i`

   - 在 web 项目使用 api ，终端执行`pnpm add @wl/api`

     - `packages/web/packages.json`

       ```json
       {
         "name": "web",
         "private": true,
         "version": "0.0.0",
         "type": "module",
         "scripts": {
           "dev": "vite",
           "build": "vite build",
           "preview": "vite preview"
         },
         "dependencies": {
           "@wl/api": "workspace:^1.0.1",
           "vue": "^3.2.45"
         },
         "devDependencies": {
           "@vitejs/plugin-vue": "^4.0.0",
           "vite": "^4.1.0"
         }
       }
       ```

6. 使用 `@wl/api`

   - `App.vue`

     - ```vue
       <script setup>
       import { getUser } from '@wl/api'
       console.log(getUser)
       </script>
       ```

7. 新建 Vue 项目，取名 `components`

   - 根目录执行 `pnpm create vite`

   - 命名为 `components`

   - 不执行 `pnpm i` 等命令

   - 清除 `components` 下除去 `packages.json` 文件的其它所有文件

   - 新建 `dir` 文件夹

   - 新建 `dir/Button.vue`

     ```vue
     <template>
       <button>我是一个按钮，我是公共的组件</button>
     </template>
     ```

   - 新建 `components/index.js`

     ```js
     import Button from './dir/Button.vue'
     
     export default Button
     ```

   - 修改 `compents/package.json`

     ```json
     {
       "name": "@wl/components",
       "private": true,
       "version": "0.0.1",
       "main": "index.js",
       "type": "module",
       "scripts": {
         "dev": "vite",
         "build": "vite build",
         "preview": "vite preview"
       },
       "dependencies": {
         "vue": "^3.2.45"
       },
       "devDependencies": {
         "@vitejs/plugin-vue": "^4.0.0",
         "vite": "^4.1.0"
       }
     }
     ```

8. 使用 `components/dir/Button`

   - `packages/web` 执行 `pnpm i @wl/components`

   - `App.vue`

     - ```vue
       <script setup>
       import { getUser } from '@wl/api'
       import Button from '@wl/components'
       console.log(getUser)
       </script>
       
       <template>
         <Button></Button>
       </template>
       
       <style scoped></style>
       ```

9. `packages/web` 执行 `pnpm run build`

   - 按道理会报错，如果有，则需要在 `components` 文件夹下执行 `pnpm i` 下载依赖后即可执行
   - `packages/web/dist` 目录下执行 `live-server` 即可跑动项目