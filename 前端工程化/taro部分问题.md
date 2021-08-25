#####  没有任何报错，但显示的结果不如预期
> 在调用小程序的 setData 方法前，Taro 会把 state 与 data 做一次 diff。由于 diff 逻辑判断属性值没有变化，不会 setData 此属性，导致视图没有更新。正确做法是在表单组件的 update 事件中 setData value 为当前值，保证 data 与表单显示值保持一致。

##### 编译模板出错(多层循环)
> 问题在于微信小程序当不指定 index 时，会隐式地注入一个名为 index 的变量名作为 index。因此这段代码在第二个循环中访问 index，实际上是当前循环的 index，而不是上级循环的 index

##### taro map循环，不能用switch或者forEacth
> 小程序中 转换模块字符串，并不是react.Node节点，所以导出编译会失效