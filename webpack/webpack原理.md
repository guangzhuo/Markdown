### webpack核心概念
- `entry` 一个可执行模块或库的入口文件。
- `chunk` 多个文件组成的一个代码块，例如把一个可执行模块和它所有依赖的模块组合和一个 chunk 这体现了webpack的打包机制。
- `loader` 文件转换器，例如把es6转换为es5，scss转换为css。
- `plugin` 插件，用于扩展webpack的功能，在webpack构建生命周期的节点上加入扩展hook为webpack加入功能。


### webpack构建流程
> 从启动webpack构建到输出结果经历了一系列过程，它们是：

- 解析`webpack`配置参数，合并从`shell`传入和`webpack.config.js`文件里配置的参数，生产最后的配置结果。
- 注册所有配置的插件，好让插件监听`webpack`构建生命周期的事件节点，以做出对应的反应。
- 从配置的`entry`入口文件开始解析文件构建`AST`语法树，找出每个文件所依赖的文件，递归下去。
- 在解析文件递归的过程中根据文件类型和`loader`配置找出合适的`loader`用来对文件进行转换。
- 递归完后得到每个文件的最终结果，根据`entry`配置生成代码块`chunk`。
- 输出所有`chunk`到文件系统。

> **需要注意的是**:在构建生命周期中有一系列插件在合适的时机做了合适的事情，比如UglifyJsPlugin会在loader转换递归完后对结果再使用UglifyJs压缩覆盖之前的结果。


### loader

1. 读取webpack.config.js配置文件的module.rules配置项，进行倒序迭代(rules的每项匹配规则按倒序匹配)
2. 根据正则匹配到对应的文件类型，同时再批量导入loader函数
3. 默认是倒序迭代调用所有的loader函数(loader从右到左，从下到上)，也可以自己来控制这个顺序
4. 最后返回处理后的代码
