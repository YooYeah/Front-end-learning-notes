### **React-loadable**

**——A higher order component for loading components with dynamic imports** 

react-loadable的思想是基于组件划分代码，按需加载我们的组件。在实际应用中，考虑如下场景：我们有一个菜单栏，不同的菜单项对应不同的渲染组件；如果我们通过import()引入模块，即使当前页面不需要渲染某个模块，它也会被加载。解决办法就是使用react-loadable实现动态加载，当切换到某一子菜单时，再加载该页面对应的组件。

react-loadable使得在组件层面拆分app像在路由层面拆分app一样简便，示例如下：

在render函数中直接返回\<LoadableComponent/>即可渲染组件。若需要传入props，可以再定义一个接受props的函数，返回\<LoadableComponent  attr = {props} />。

```
import Loadable from 'react-loadable';

const LoadableComponent = Loadable({
  loader: () => import('./my-component'),
  loading: () => "loading",
});
```
或省略loader、loading：
```
import Loadable from 'react-loadable';

function MyLoadingComponent() {
  return <div>Loading...</div>;
}

const LoadableAnotherComponent = Loadable(
  () => import('./another-component'),
  MyLoadingComponent
);
```

Loadable是一个高阶组件，只需传入要加载的组件以及等待加载组件时的“Loading”组件。LoadingComponent还可以接受props作为错误提示，应对组件加载失败的情况：

```
function MyLoadingComponent({ error }) {
  if (error) {
    return <div>Error!</div>;
  } else {
    return <div>Loading...</div>;
  }
}
```

Loadable还接收第三个参数delay，默认是200ms，也可接受用户的自定义输入。当加载速度小于200ms时，加载中的样式会一闪而过。为了解决加载闪烁问题，LoadingComponent引入另一个参数pastDelay，当组件的加载时间比设定的delay时间长时，LoadingComponent才会显示，否则返回null。

```
export default function MyLoadingComponent({ error, pastDelay }) {
  if (error) {
    return <div>Error!</div>;
  } else if (pastDelay) {
    return <div>Loading...</div>;
  } else {
    return null;
  }
}
```

此外，Loadable组件还暴露了一个用于预加载的静态方法：

```
LoadableMyComponent.preload();
```



资料：

[react-loadable](https://github.com/jamiebuilds/react-loadable)

[react-loadable 中文版](https://zhuanlan.zhihu.com/p/25874892)