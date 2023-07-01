## 新建vue3

```
npm init vite@latest
```

## 无法解析

![image-20230519092354695](D:\图\typora\vue3问题\image-20230519092354695.png)

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