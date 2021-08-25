# webpack

### 常用插件
```
  1.@babel/code:
  2.@babel/preset-env
  3.@babel/preset-react:解析react
  4.babel-loader：解析js
  5.mini-css-extract-plugin:将CSS提取为独立的文件的插件，对每个包含css的js文件都会创建一个CSS文件，支持按需加载css和sourceMap，添加文件指纹（与style-loader方式不同，一个是提取，一个注入）；
  6.optimize-cssassets-webpack-plugin:压缩css；
  7.html-webpack-plugin:html 压缩；
  8.clean-webpack-plugin 清除目录
  9.postcss-loader和autoprefixer:css前缀兼容
  10.@babel/plugin-syntax-dynamic-import 动态import
  11.friendly-errors-webpack-plugin
  
```
- 新建`babelrc`，用于babel 定义配置
```js
	// babelrc/
  {
    "presets": [
      "@babel/preset-env",
      "@babel/preset-react"
     ]
   }
```

```js
指纹：
		hash(8-16|空)：文件修改，整个项目构建hash会变更,图片，字体：hash
    chunkhash(8-16|空)：和webpack打包chunk（模块）有关，不同entry不同chunkhash,webpack文件修改：chunkhash
    contenthash(8-16|空)：文件内容修改,css,js:contenthash
```
-	html-webpack-plugin，压缩html，设置压缩参数
 ```
 new htmlWebpackPlugin({
   template: path.join(__dirname, './src/search.html'),
   	filename:'search.html',
   	chunks:['search' ],
   	inject:true,
   	minify:{
   		html5:true,
   		collapseWhitespace:true,
   		preserveLineBreaks:false,
   		minifyCSS:true,
   		minifyJS:true,
   		removeComments:true
   	}
})
 ```
 - 自动清理 dist文件

```js
plugins:[
   new cleanWebpackPlugin(),
]
```

- 添加css 样式兼容
```js
{
loader: 'postcss-loader',
	options:{
	plugins:() => [
			require('autoprefixer')({
			overrideBrowserslist:['last 2 version','> 1%','ios 7']
		})
	]
	}
}
```

- sourceMap
[参考资料]https://segmentfault.com/a/1190000016404266?utm_source=tag-newest

- tree shaking(摇树优化)

 	概念：1个模块多个方法时，只要用到某个方法，则整个文件都会打到bundle里面，没用到的方法会在uglify阶段擦除掉

 	使用：webpack 默认支撑在`.babelrc`里设置`modules:false`,生成环境默认开启

- Scope Hoisting
	产生的问题：构建模块打包后，引入的模块和打包后会形成大量的IIFE（匿名闭包），在构建当初会形成占用内存资源开销；
	对比：通过scope hoisting 可以减少函数声明和内存开销
	
- 构建基础设计 

  <!--<img src="/Users/zhengguangzhuo/Desktop/构建包功能设计.jpeg" alt="构建包功能设计" style="zoom:50%;" />-->

- 目录结构设计

  <!--<img src="/Users/zhengguangzhuo/Desktop/目录机构设计.jpg" alt="目录机构设计" style="zoom:50%;" />-->

