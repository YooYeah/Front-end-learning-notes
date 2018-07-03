### Classnames

install:

```
npm install classnames --save
```

or

```
yarn add classnames
```

该库可为我们的element动态增加class，接受的输入包括字符串string或对象object，字符串'foo'等同于对象：{foo:true}。如果key对应的value是false，那么该key就不会作为元素的className。

```
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'

// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'

// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'
```

我们也可以传入数组，传入的数组会被递归扁平化：

```
var arr = ['b', { c: true, d: false }];
classNames('a', arr); // => 'a b c'
```

可与字符串拼接结合生成动态类名：

```
let buttonType = 'primary';
classNames({ [`btn-${buttonType}`]: true });
```

绑定bind (用于[css-modules](https://github.com/css-modules/css-modules))

```
var classNames = require('classnames/bind');
 
var styles = {
  foo: 'abc',
  bar: 'def',
  baz: 'xyz'
};
 
var cx = classNames.bind(styles);
 
var className = cx('foo', ['bar'], { baz: true }); // => "abc def xyz"
```

再与less结合使用，根据类名来控制样式：

```
import style from "./style.less";
import cx from "classnames";

...

className={cx(style.container, {addName: true})}
```

在less文件中控制\<element\>组件的样式：

```
:local(.container) {
  &.addName {
    element {
      pointer-events: none;
    }
  }
}
```





参考：

[npm classnames](https://www.npmjs.com/package/classnames)

[css-modules](https://github.com/css-modules/css-modules)