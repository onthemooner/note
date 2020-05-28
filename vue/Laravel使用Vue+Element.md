# Laravel使用Vue+Element

## 1.安装npm

```bash
sudo apt install npm
```

## 2.使用淘宝镜像

检查当前镜像

```bash
npm config get registry
```

```bash
npm config set registry https://registry.npm.taobao.org
```

## 3.安装Vue并安装其他模块

```bash
npm install vue
```

在项目目录下运行

```bash
npm install
```

## 4.安装Element

先更新corejs

```bash
npm install --save core-js@^3
```

安装Element

```bash
npm i element-ui -S
```

## 5.引用

新建`resources\js\components\App.vue`

```html
<template>
    <div>
        <h1>Hello, Larvuent!</h1>
        <el-button @click="visible = true">Button</el-button>
        <el-dialog :visible.sync="visible" title="Hello world">
            <p>Try Element</p>
        </el-dialog>
    </div>
</template>

<script>
export default {
    data() {
        return {
            visible: false
        };
    }
};
</script>

<style>
.hello {
    font-size: 2em;
    color: green;
}
</style>
```

新建`resources\js\app.js`

```js
require('./bootstrap');

import Vue from 'vue';
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
import App from './components/App.vue';

Vue.use(ElementUI);

const app = new Vue({
    el: '#app',
    render: h => h(App)
});
```

新建`resources\views\vue.blade.php`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Element</title>
</head>
<body>
    <div id="app"></div>
    <script src="{{ mix('js/app.js') }}"></script>
</body>
</html>
```

新建增路由

```php
Route::get('vue', function () {
    return view('vue');
});
```

## 6.打包

使用Laravel自带的Laravel-mix打包

```bash
npm run dev
```

`app.js`和`app.css`生成成功，访问路由查看效果

## 6.CSRF保护

在网页头部添加

```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```
