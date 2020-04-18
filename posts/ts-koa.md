---
title: '使用TypeScript编写Koa应用'
date: 2020-01-30 14:44:03
tags: [TypeScript,Koa]
published: true
hideInList: false
feature: 
isTop: false
---
## 1.初始化node项目
```shell
# 初始化项目
npm init
# 安装TypeScript
npm i typescript -D
# 新建src目录
mkdir src
# 初始化TS配置
tsc --init
```


## 配置tsconfig.json
[如何配置？](https://www.typescriptlang.org/v2/en/tsconfig)
```json
// /tsconfig.json
{
  "compilerOptions": {
    "target": "ES2017", 
    "module": "commonjs",
    "lib": ["ESNext"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true, 
    "moduleResolution": "node",
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```


## 安装项目依赖
```shell
npm i koa koa-router koa-body
npm i @types/node @types/koa @types/koa-router -D  # 类型声明
```


## 编写最简Koa项目
在`src`目录下新建文件`index.ts`
```javascript
// /src/index.ts
import Koa from 'koa';
import KoaBody from 'koa-body';
const app = new Koa();
app.use(KoaBody());
app.use(async (ctx) => {
    ctx.body = 'Hello, world!';
});
const PORT = process.env.PORT || 8080;
app.listen(PORT, () => {
    console.log(`The server is running at port ${PORT}`);
});
```
编译并执行
```shell
tsc
node ./dist/index.js
```
访问`http://localhost:8080`

## 编写路由文件
新建目录`routes`，并在目录下新建路由文件入口`index.ts`
```javascript
// /src/routes/index.ts
import KoaRouter from 'koa-router';
import { readdirSync } from 'fs';
import { join, basename } from 'path';

const router = new KoaRouter();
const files = readdirSync(__dirname);
const filename = basename(__filename);

files.forEach(async file => {
  if (file !== filename) {
    const r = (await import(join(__dirname, file))).default;
    router.use(r.routes()).use(r.allowedMethods());
  }
});

export default router;
```
新建`about.ts`路由文件
```javascript
// /src/routes/about.ts
import KoaRouter from 'koa-router';

const router = new KoaRouter({
  prefix: '/about',
});

router.get('/', async (ctx) => {
  ctx.body = 'About';
});

export default router;
```
最后修改一下主文件`index.ts`，引入路由
```javascript
// /src/index.ts
import Koa from 'koa';
import KoaBody from 'koa-body';
import routers from './routes';

const app = new Koa();
app.use(KoaBody());

app.use(routers.routes()).use(routers.allowedMethods());

const PORT = process.env.PORT || 8080;
app.listen(PORT, () => {
    console.log(`The server is running at port ${PORT}`);
});
```
编译并执行
```shell
tsc
node ./dist/index.js
```
访问`http://localhost:8080/about`


## 补充
### 在ctx上挂载自定义属性
新建`types/index.d.ts`类型声明文件
```javascript
// /src/typings/index.d.ts
import { DefaultContext } from 'koa';
export interface CustomContext extends DefaultContext {
    custom: string
}
```
在`new Koa()`时传入定义的接口，示例如下
```javascript
// /src/index.ts
import Koa, { DefaultState } from 'koa';
import { CustomContext } from './typings';

const app = new Koa<DefaultState, CustomContext>();
app.context.custom = '666';
app.use(async (ctx, next) => {
    console.log(ctx.custom);
    await next();
});
```
使用路由时，也需将接口传入，以便在路由中使用自定义属性
```javascript
// /src/routes/about.ts
import KoaRouter from 'koa-router';
import { DefaultState } from 'koa';
import { CustomContext } from '../typings';

const router = new KoaRouter<DefaultState, CustomContext>({
  prefix: '/about',
});

router.get('/', async (ctx) => {
  ctx.body = ctx.custom;
});
export default router;
```
自定义中间件同理

### 在global上挂载自定义属性
在 .d.ts 文件中声明
```javascript
// /src/typings/index.d.ts
declare global {
  namespace NodeJS {
    interface Global {
      app: any
    }
  }
}
```

### 调试
```shell
npm i ts-node-dev -D
ts-node-dev --clear ./src/index.ts
```