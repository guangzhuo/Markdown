### 流程图
![image](http://user-gold-cdn.xitu.io/2019/2/23/169197bbdc7ae14e?imageslim)

- `partialState`：`setState`传入的第一个参数，对象或函数
- `_pendingStateQueue`：当前组件等待执行更新的`state`队列
- `isBatchingUpdates`：`react`用于标识当前是否处于批量更新状态，所有组件公用
- `dirtyComponent`：当前所有处于待更新状态的组件队列
- `transcation`：`react`的事务机制，在被事务调用的方法外包装n个`waper`对象，并一次执行：`waper.init`、被调用方法、`waper.close`
- `FLUSH_BATCHED_UPDATES`：用于执行更新的`waper`，只有一个`close`方法

### 总结

#### 1.钩子函数和合成事件中：

在`react`的生命周期和合成事件中，`react`仍然处于他的更新机制中，这时`isBranchUpdate`为`true`。
按照上述过程，这时无论调用多少次`setState`，都会不会执行更新，而是将要更新的`state`存入`_pendingStateQueue`，将要更新的组件存入`dirtyComponent`。
当上一次更新机制执行完毕，以生命周期为例，所有组件，即最顶层组件`didmount`后会将`isBranchUpdate`设置为`false`。这时将执行之前累积的`setState`。


#### 2.异步函数和原生事件中

由执行机制看，`setState`本身并不是异步的，而是如果在调用`setState`时，如果`react`正处于更新过程，当前更新会被暂存，等上一次更新执行后在执行，这个过程给人一种异步的假象。
在生命周期，根据JS的异步机制，会将异步函数先暂存，等所有同步代码执行完毕后在执行，这时上一次更新过程已经执行完毕，`isBranchUpdate`被设置为`false`，根据上面的流程，这时再调用`setState`即可立即执行更新，拿到更新结果。


### 3.partialState合并机制

我们看下流程中_processPendingState的代码，这个函数是用来合并state暂存队列的，最后返回一个合并后的state。



```
_processPendingState: function (props, context) {
    var inst = this._instance;
    var queue = this._pendingStateQueue;
    var replace = this._pendingReplaceState;
    this._pendingReplaceState = false;
    this._pendingStateQueue = null;

    if (!queue) {
      return inst.state;
    }

    if (replace && queue.length === 1) {
      return queue[0];
    }

    var nextState = _assign({}, replace ? queue[0] : inst.state);
    for (var i = replace ? 1 : 0; i < queue.length; i++) {
      var partial = queue[i];
      _assign(nextState, typeof partial === 'function' ? partial.call(inst, nextState, props, context) : partial);
    }

    return nextState;
  },
```

### 4.`componentDidMount`调用`setstate`
> 在componentDidMount()中，你 可以立即调用setState()。它将会触发一次额外的渲染，但是它将在浏览器刷新屏幕之前发生。这保证了在此情况下即使render()将会调用两次，用户也不会看到中间状态。谨慎使用这一模式，因为它常导致性能问题。在大多数情况下，你可以 在constructor()中使用赋值初始状态来代替。然而，有些情况下必须这样，比如像模态框和工具提示框。这时，你需要先测量这些DOM节点，才能渲染依赖尺寸或者位置的某些东西。


以上是官方文档的说明，不推荐直接在`componentDidMount`直接调用`setState`，由上面的分析：`componentDidMount`本身处于一次更新中，我们又调用了一次`setState`，就会在未来再进行一次`render`，造成不必要的性能浪费，大多数情况可以设置初始值来搞定。
当然在`componentDidMount`我们可以调用接口，再回调中去修改`state`，这是正确的做法。
当`state`初始值依赖dom属性时，在`componentDidMount`中`setState`是无法避免的。

### 5.componentWillUpdate componentDidUpdate
这两个生命周期中不能调用setState,在它们里面调用setState会造成死循环，导致程序崩溃。

### 6.推荐使用方式

在调用setState时使用函数传递state值，在回调函数中获取最新更新后的state。