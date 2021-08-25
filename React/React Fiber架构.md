**任务调度，协程权限交换**

> 简单的说，一个Fiber就是一个POJO对象，代表了组件上需要做的工作。一个React Element可以对应一个或多个Fiber节点

> React源码中的注释: A Fiber is work on a Component that needs to be done or was done. There can be more than one per component.