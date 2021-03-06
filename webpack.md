# webpack

## 什么是webpack？
webpack 是一个 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)（在webpack中一切皆模块），用于映射到项目需要的每个模块，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle（浏览器可识别的一个或多个文件）。

## webpack和其他前端自动化构建工具的区别？
* gulp：前端自动化构建工具，以任务的形式进行构建，css的编译，js编译，启动服务，手动配置，对文件进行操作
* grunt：类似于gulp也是自动化的构建工具，减轻手动构建效率低下的问题
* webpack：以模块为入口，使用loader，plugins对文件进行编译，在webpack中一切皆模块

## webpack的核心概念
* 模块解析规范，在webpack中模块是按照node.js commonJs规范定义，也可以自己配置（resolve）
* 入口（entry）
* 出口（output）
* 解析规则（loader）
* 插件（plugins）
* devServer

## 安装
```
npm install webpack webpack-cli -g 
```
或者
```
yarn global add webpack webpack-cli
```
然后就可以全局执行命令了
webpack-cli 是使用 webpack 的命令行工具，在 4.x 版本之后不再作为 webpack 的依赖了，我们使用时需要单独安装这个工具。

## 入口 entry
入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。
语法：
entry: string | [string] | object { <key>: string | [string] } | (function: () => string | [string] | object { <key>: string | [string] })
传递一个字符串代表一个入口
传递数组{Array}或者对象{Object}，代表一个或者多个入口

示例
```
  module.exports = {
    entry: 'app.js'
  }
```
或者
```
  module.exports = {
    entry: {
      app: 'app.js'
    }
  } 
```

## 出口（output）
所有构建目标的输出位置
语法：
```
output: {
  path: path.reslove(__dirname, 'dist'), // output目录对应一个绝对路径
  publicPath: '/', // 此输出目录对应公开的URL
  filename: 'js/bundle.js' // 对应js输出位置，以及bundle后文件名
  chunkFilename: '[id].[hash].js' // 对应使用懒加载模块的输出名称
}
```
## filename中的模板标识符
* [name] 模块名称 如果在入口指定名称，那么name就是指定的名称，如果没有指定就是main
* [hash] 模块标识符的hash sauidashudhi2137128hqisdihqq 唯一版本标识 [hash:6] 取6位
* [id] 模块的id值
* [ext] 对应文件后缀

## 模块处理（moudle）
处理项目中不同类型的模块。
### module.noParse
* 作用：忽略一些不需要编译的内容
* 语法：
```
  RegExp | [RegExp]
  RegExp | [RegExp] | function（从 webpack 3.0.0 开始）
```

* 实例
```
  module: {
    noParse: /jquery|lodash/,

    // 从 webpack 3.0.0 开始
    noParse: function(content) {
      return /jquery|lodash/.test(content);
    }
  }
```

## rules（编译规则，用来配置loader）[array]
- 如果你想用es6，scss，less，postcss，......但是浏览器不支持，所以需要编译，在webpack中除了模块规范外，还可以通过loader实现类似于gulp这类工具，自动化编译功能
- webpack中提供一种处理多种文件格式的机制，便是使用loader。我们可以把loader理解为是一个转换器，负责把某种文件格式的内容转换成浏览器可识别的内容。

* loader匹配规则
```
  module: {
    rules: [
      {
        test: /.js$/, //匹配需要编译的文件类型 条件1
        include: [path.resolve(__dirname,'src'),...] // 需要被匹配的范围 条件2
        exclude: [path.resolve(__dirname,'node_modules'),...] // 不需要被匹配的范围 条件3
        use: [
          'style-loader', // loader名称
          {
            loader: 'css-loader', // loader名称
            options: { // loader传递的参数
              importLoaders: 1
            }
          },
          {
            loader: 'less-loader',
            options: {
              noIeCompat: true
            }
          }
        ]
      }
    ]
  }
```
* 常用loader
```
babel-loader  url-laoder  css-loader style-loader  sass-loader less-loader postcss-loader .....
```

## 插件  (plugins)
除了编译任务外的其他任务

## 模块查找规范
webpack默认使用nodejs commonJs模块规范，但是也可以自定义一些解析规范
- 如果写的是模块名称，会去node_modules目录下查找
- 如果写的是相对路径（文件直接查找）（文件夹找文件夹下的package.json文件中main字段对应的文件，如果没有package，自动查找目录下的index文件）

* resolve  配置模块如何解析
* 参数
* alias  配置别名
```
resolve: {
 alias: {
   '@': path.resolve(__dirname, './src')
 }
}
```
* extensions 自动解析确定的扩展（默认情况下只有js后缀可以被忽略不写，先在可以通过extensions字段进行配置可忽略的后缀）
```
resolve: {
 extensions: ['.vue', '.json', '.js']
}
```
* modules  默认情况下直接模块名称回去node_modules目录下查找，但是现在在webpack中可以使用modules字段配置模块目录
```
resolve: {
 modules: ['node_modules', path.resolve(__dirname, './src/module')]
}
```

## 环境区分
在构建工具搭建的时候一般我们都需要区分环境，因为不同环境之间构建差异不一样，一般都需做开发环境和生产环境

### 开发环境
* 启动静态服务
* 实时更新，热更新
* 文件编译
* 需要调试
* mock数据

### 生产环境
* 优化
* 压缩
* 不需要服务
* 不需要热更新
* 需要文件分离
