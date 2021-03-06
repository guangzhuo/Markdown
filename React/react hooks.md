### 为什么使用Hooks？

1. React Hooks 就是让你不必写class组件就可以用state和其他的React特性；
2. 你也可以编写自己的hooks在不同的组件之间复用；
3. 由于业务变动，函数组件不得不改为类组件等等。



#### React hooks优势:

1. 没有破坏性改动完全可选的。 你无需重写任何已有代码就可以在一些组件中尝试 Hook。100% 向后兼容的。 Hook 不包含任何破坏性改动。
1. 更容易复用代码:它通过自定义hooks来复用状态，从而解决了类组件有些时候难以复用逻辑的问题
1. 函数式编程风格:函数式组件、状态保存在运行环境、每个功能都包裹在函数中，整体风格更清爽，更优雅
1. 代码量更少
1. 更容易拆分组件

#### React hooks缺点

==hooks 是 React 16.8 的新增特性、以前版本的就别想了==
1.部分代码从主动式变成响应式
2.状态不同步:函数的运行是独立的，每个函数都有一份独立的作用域。当我们处理复杂逻辑的时候，经常会碰到“引用不是最新”的问题