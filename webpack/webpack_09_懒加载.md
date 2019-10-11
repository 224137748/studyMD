## webpack 09 懒加载   & 什么是chunk

#### 1、懒加载

​	进入web首页时只加载首页的资源文件，其他模块的内容根据业务需求进行异步加载，优化用户体验，提升首屏加载速度，提升项目性能。

```js
// js文件中
// 不执行getComponent函数，就不会加载 loadsh 文件
function getComponent() {
    return import(/* webpackChunkName: "lodash" */ './js/lodash.js' ).then(({default: _}) => {
        var ele = document.createElement('div');
 		ele.innerHTML = _.join(['join', 'lee'], '-');
        return ele;
    })
}
document.addEventListener('click', () => {
    getComponent().then(ele => {
        document.body.appendChild(ele);
    })
})

```

再举一个在`Vue`中使用懒加载实现异步加载组件的示例：

```js
// router.js
import Router from 'vue-router';
import Home from './src/pages/home.vue';
import User from './src/pages/user.vue';
const routes = [{
    path:'/home',
    component: Home,
    name: 'home'
}, {
    path: '/user',
    name: 'user',
    component: () => import(/* webpackChunkName: "user" */ './src/pages/user.vue')
    // component: resolve => require(['./src/pages/user.vue'], resolve)
}]

```

上面展示了使用`require、import`两种方法实现异步组件加载的过程。

#### 2、chunk

​	在`webpack`打包的过程中，没生成的每个独立的`js`文件称之为`chunk`