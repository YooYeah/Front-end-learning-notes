#### Styled Components

##### ——css in js

在React web应用中，当样式比较简单时，我们可以直接在标签内定义：

```
<div style={{ fontSize: '2em' }}> Hello World! </div>
```

但当我们需要设置大量样式时，这显然不是一个好选择。除了编写额外的css文件引入，我们还可以使用styled-component。styled-components是React一个第三方css in js库，可以将样式写成更具语义化的组件的形式，摆脱className属性，实现在js上编写css。

安装：

```
npm install --save styled-components
```

在js/ts文件中import styled：

```
import styled from 'styled-components'
```

- **基本用法**

若我们需要定义一个div，添加样式“font-size:2em”，使用styled-components：

```
const Wrapper = styled.div`
	font-size: 2em;
`
```

我们定义了一个常量Wrapper，使用es6的标签模板和模板字符串，styled.div是一个标签模板函数，返回一个React Component，styled-components会为这个React Component添加一个随机字符串作为class（这一点可以解决传统CSS的全局作用域问题，为了不让选择器互相冲突，我们通常需要计算权值或使用后代选择器；但由于styled-components生成的字符串是随机的，组件之间的className不会冲突，我们不需担心覆盖问题）；其后跟着模板字符串参数，模板字符串其实就是CSS样式，会添加到该React Component中。 

接着我们便可以用Wrapper代替之前定义的div：

```
<Wrapper> Hello World! </Wrapper>
```

使用styled-components编写样式时，我们不需要className充当“中间人”，移除了组件和样式之间的映射关系，编写css看起来像是编写一个常规的React组件，并把我们需要的样式绑定到该组件上。styled-components分离了组件和容器，components组件是指最简单的只包含样式的结构，即“how things look”；Containers容器包含业务逻辑或者复杂生命周期方法，但不包含样式，即“how things work”。

- **基于props控制样式**

若我们需要为某个组件定义不同的样式，传统做法是引入不同的className，对应不同的style：

```
class Title extends React.Component {
    render() {
        const className = `title${this.props.primary ? ' title-primary' : ''}`;
        return (
            <div className={className}>{this.props.children}</div>
        );
    }
}

// 若组件有primary属性，则渲染"title-primary"类的样式
<Title primary> Hello world! </Title>
```

styled-components控制样式使用的是props，由于在组件中传入的所有props都可以在定义样式组件时获取到，因此我们可以直接在样式组件中基于props来控制组件，使React组件更加简洁：

```
const Wrapper = styled.div `
	font-size: 2em;
	color: ${props => props.promary ? 'white' : 'red' };
`
```

- **其他CSS特性**

styled-components支持CSS的所有样式，比如伪类、媒体查询、keyframes等。

```
const Wrapper = styled.div`
	font-size: 2em;
	&:hover {
        font-size: 5em;
	}
`
```

（&的用法很像less的嵌套规则，对伪类如:hover、:active的书写尤其有用）

CSS动画支持：

```
import { keyframes } from 'styled-components';
const fadeIn = keyframes`
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
`;

const FadeInButton = styled.button`
  animation: 1s ${fadeIn} ease-out;
`;
```

如果需要注入全局样式，可以使用“injectGlobal”：

```
import styled, { injectGlobal } from 'styled-components';
injectGlobal`
  @font-face {
    font-family: 'Operator Mono';
    src: url('../fonts/Operator-Mono.ttf');
  }

  body {
    margin: 0;
  }
`;
```

- **组件样式继承**

有时我们需要修改某个组件的某一样式来适应不同的case，比如页面按钮的样式是固定的，但有一个按钮需要修改背景颜色，在传统的CSS中，我们会用类似 `class="fruit tomato"` 的写法来复用class定义。styled-components提供了“extend”语法，利用js的继承实现样式的复用：

```
const Button = styled.button`
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

const TomatoButton = Button.extend`
  color: tomato;
  border-color: tomato;
`;
```

组件内部会执行合并操作，子组件中的属性会覆盖父组件中同名的属性。

- **附加额外属性**

styled-components还提供了 `.attr` 构造器为组件提供额外的属性。使用这个特性我们可以为组件添加静态属性、动态属性或传递第三方库的属性：

```
const Input = styled.input.attrs({
  // we can define static props
  type: 'password',

  // or we can define dynamic ones
  margin: props => props.size || '1em',
  padding: props => props.size || '1em'
})`
  color: palevioletred;
  font-size: 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;

  /* here we use the dynamically computed props */
  margin: ${props => props.margin};
  padding: ${props => props.padding};
`;

render(
  <div>
    <Input placeholder="A small text input" size="1em" />
    <br />
    <Input placeholder="A bigger text input" size="2em" />
  </div>
);
```

我们还可以自己定义className:

```
const Button = styled.button.attrs({
  className: 'small',
})`
  background: black;
  ...
`;
```

编译后的html结构如下：

```
<button class="sc-gPEVay small gYllyG">
  Styled Components
</button>
```

这样我们就可以识别自己定义的class，方便进行后续操作。



**参考资料：**

[styled-component官方文档](https://www.styled-components.com/docs)

[使用 styled-components 加速 React 开发](https://zhuanlan.zhihu.com/p/28876652)