## 新建vue3

```
npm init vite@latest
```

## 无法解析

![image-20230519092354695](D:\notes\typora\vue3问题\image-20230519092354695.png)

> typeScripe无法解析vue文件

- src下新建env.d.ts文件
- 复制代码

```typescript
/// <reference types="vite/client" />

declare module '*.vue' {
    import { DefineComponent } from 'vue'
    // eslint-disable-next-line @typescript-eslint/no-explicit-any, @typescript-eslint/ban-types
    const component: DefineComponent<{}, {}, any>
    export default component
  }
  
```

## 配置@

1. 安装node申明（如果报错的话）

> npm i --save-dev @types/node

2. vite.config.ts

```typescript
import path from 'path';
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    // 配置路径别名
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});

```

3. tsconfig.json======>paths` 和 `baseUrl

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "target": "esnext",
    "module": "esnext",
    "moduleResolution": "node",
    "strict": true,
    "jsx": "preserve",
    "sourceMap": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "lib": ["esnext", "dom"],
    "baseUrl": ".",
    "paths": {
      "@/*": [
        "src/*"
      ]
    },
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"]
}

```

4. 重启

## 代理配置

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  base: "./",//打包路径
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')//设置别名
    }
  },
  server: {
    open: true,//启动项目自动弹出浏览器
    port: 4000,//启动端口
    proxy: {
      '/api': {
        target: 'http://localhost:8080',	//实际请求地址
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      },
    }
  }
  // server: {
  //   port: 4000,//启动端口
  //   open: true,
  //   proxy: {
  //     // 跨域简单写法写法
  //     '/api': 'http://123.56.85.24:5000'//代理网址
  //   },
  //   cors: true
  // },
})


```

