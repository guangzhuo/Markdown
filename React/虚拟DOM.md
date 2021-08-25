### React组件的渲染流程

- 使用`React.createElement`或`JSX`编写`React`组件，实际上所有的`JSX`代码最后都会转换成`React.createElement(...)`，`Babel`帮助我们完成了这个转换的过程。


- `createElement`函数对`key`和`ref`等特殊的`props`进行处理，并获取`defaultProps`对默认`props`进行赋值，并且对传入的孩子节点进行处理，最终构造成一个`ReactElement`对象（所谓的虚拟`DOM`）。


- `ReactDOM.render`将生成好的虚拟`DOM`渲染到指定容器上，其中采用了批处理、事务等机制并且对特定浏览器进行了性能优化，最终转换为真实`DOM`


### 虚拟DOM的组成

即`ReactElementelement`对象，我们的组件最终会被渲染成下面的结构：

- `type`：元素的类型，可以是原生`html`类型（字符串），或者自定义组件（函数或class）
- `key`：组件的唯一标识，用于`Diff`算法，下面会详细介绍
- `ref`：用于访问原生`dom`节点
- `props`：传入组件的`props`，`chidren`是`props`中的一个属性，它存储了当前组件的孩子节点，可以是数组（多个孩子节点）或对象（只有一个孩子节点）
- `owner`：当前正在构建的`Component`所属的`Component`
- `self`：（非生产环境）指定当前位于哪个组件实例
- `_source`：（非生产环境）指定调试代码来自的文件`(fileName)`和代码行数`(lineNumber)`


### 防止XSS
`ReactElement`对象还有一个`?typeof`属性，它是一个`Symbol`类型的变量`Symbol.for('react.element')`，当环境不支持`Symbol`时，`?typeof`被赋值为`0xeac7`。
这个变量可以防止`XSS`。如果你的服务器有一个漏洞，允许用户存储任意`JSON`对象， 而客户端代码需要一个字符串，这可能为你的应用程序带来风险。JSON中不能存储`Symbol`类型的变量，而`React`渲染时会把没有`?typeof`标识的组件过滤掉。


### 批处理和事务
React在渲染虚拟DOM时应用了批处理以及事务机制，以提高渲染性能。[setState的执行机制》来源：掘金](https://juejin.im/post/6844903781813993486)


### 针对性的性能优化
在`IE（8-11`）和`Edge`浏览器中，一个一个插入无子孙的节点，效率要远高于插入一整个序列化完整的节点树。
`React`通过`lazyTree`，在`IE（8-11`）和`Edge`中进行单个节点依次渲染节点，而在其他浏览器中则首先将整个大的`DOM`结构构建好，然后再整体插入容器。
并且，在单独渲染节点时，`React`还考虑了`fragment`等特殊节点，这些节点则不会一个一个插入渲染。

### 虚拟DOM事件机制
`React`自己实现了一套事件机制，其将所有绑定在虚拟`DOM`上的事件映射到真正的`DOM`事件，并将所有的事件都代理到`document`上，自己模拟了事件冒泡和捕获的过程，并且进行统一的事件分发。
`React`自己构造了合成事件对象`SyntheticEvent`，这是一个跨浏览器原生事件包装器。 它具有与浏览器原生事件相同的接口，包括`stopPropagation()`和`preventDefault()`等等，在所有浏览器中他们工作方式都相同。这抹平了各个浏览器的事件兼容性问题。

