### CSS Modules

CSS使用全局选择器机制来设置样式，很容易造成全局污染的问题，权重的计算也比较复杂，样式变多后会导致命名混乱，不够灵活。CSS Modules是CSS模块化的一个解决方案。它的本质是一个css文件，并把类名都默认限定到局部作用域中。

CSS模块编译为一种名为ICSS或可 [Interoperable CSS](https://github.com/css-modules/icss) 的低层次交换格式，分别对应:import和:export两个新增的伪类来解决样式导入和导出的问题。但它们的编写方式与普通CSS文件一样: 

```
/* style.css */
.className {
  color: green;
}
```

```
import styles from "./style.css";
// import { className } from "./style.css";

element.innerHTML = '<div class="' + styles.className + '">';
```

结合Webpack的css-loader，可以实现用JS来管理CSS，在CSS中定义样式，在JS中导入。按如下方式启用CSS Modules:

```
// webpack.config.js
module.exports = {
  entry: __dirname + '/index.js',
  output: {
    publicPath: '/',
    filename: './bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'stage-0', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: "style-loader!css-loader?modules"
      },
    ]
  }
};
```
css-loader后面加上了一个查询参数modules，表示打开CSS Modules功能。

```
// webpack.config.js
css?modules&localIdentName=[name]__[local]-[hash:base64:5]
```

同理，加上modules即为启用，`localIdentName` 是设置生成样式的命名规则。 CSS Modules之后会按照这个规则自动生成唯一的class名。默认的哈希算法是[hash:base64]。

与React组件结合使用示例：

```
// App.css
.title {
  color: red;
}
```

在下述App.js代码中，将样式文件App.css输入到style对象，style.title代表了一个class。title是我们在css文件中的一个类名，构建工具在之后会根据我们设定的命名规则将style.title编译成一个哈希字符串。App.css同样也会被编译为对应的字符串。

```
// App.js
import React from 'react';
import style from './App.css';

export default () => {
  return (
    <h1 className={style.title}>
      Hello World
    </h1>
  );
};
```

CSS Modules总有全局作用域和局部作用域的概念，样式默认都是局部的，下面两种写法是等价的：

```
.title {
  color: red;
}

// 等价于

:local(.title) {
  color: red;
}
```

如果想要切换到全局模式，使用`:global(.className)`语法，这样声明的class不会被编译成哈希字符串。

```
// 局部模式
:local(.title) {
  color: red;
}

// 全局模式
:global(.title) {
  color: red;
}
```

在js文件中，使用普通的class的写法就会引用全局的class。

```
import React from 'react';
import styles from './App.css';

export default () => {
  return (
  	{/* 引用全局的class */}
    <h1 className="title">
      Hello World
    </h1>
    {/* 引用局部的class */}
    <h1 className={style.title}>
      Hello World
    </h1>
  );
};
```

CSS Modules还可以使用composes来组合复用样式。

在css文件中，可以让样式继承：

```
/* Button.css */
.base { /* 所有通用的样式 */ }

.normal {
  composes: base;
  /* normal 其它样式 */
}

.disabled {
  composes: base;
  /* disabled 其它样式 */
}
```

由于在 `.normal` 中 composes 了 `.base`， 在js文件中，编译后button会具有两个class。

```
import styles from './Button.css';

buttonElem.outerHTML = `<button class=${styles.normal}>Submit</button>`
```

选择器也可以继承其他CSS文件里的规则。

```
/* settings.css */
.primary-color {
  color: #f40;
}

/* components/Button.css */
.base { /* 所有通用的样式 */ }

.primary {
  composes: base;
  composes: primary-color from './settings.css';
  /* primary 其它样式 */
}
```

CSS Modules可以与classnames库更好地结合使用：

```
/* dialog.css */
.root {}
.confirm {}
.disabledConfirm {}
```

```
import classNames from 'classnames';
import styles from './dialog.css';

export default class Dialog extends React.Component {
  render() {
    const cx = classNames({
      [styles.confirm]: !this.state.disabled,
      [styles.disabledConfirm]: this.state.disabled
    });

    return <div className={styles.root}>
      <a className={cx}>Confirm</a>
      ...
    </div>
  }
}
```

如果不想频繁地使用styles.className，可以参考 [react-css-modules](https://github.com/gajus/react-css-modules) 。

```
import React from 'react';
import CSSModules from 'react-css-modules';
import styles from './table.css';

class Table extends React.Component {
    render () {
        return <div styleName='table'>
            <div styleName='row'>
                <div styleName='cell'>A0</div>
                <div styleName='cell'>B0</div>
            </div>
        </div>;
    }
}

export default CSSModules(Table, styles);
```



官网：[css-modules](https://github.com/css-modules/css-modules)

资料：[CSS Modules 详解及 React 中实践](https://github.com/camsong/blog/issues/5)