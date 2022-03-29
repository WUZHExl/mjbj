![image-20220306143321751](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220306143321751.png)

### 1.简单配置

> 该部分需要掌握：
>
> 1. Webpack 常规配置项有哪些？
> 2. 常用 Loader 有哪些？如何配置？
> 3. 常用插件（Plugin）有哪些？如何的配置？
> 4. Babel 的如何配置？Babel 插件如何使用？

```js
//webpack五个核心概念
module.exports = {
  // webpack配置
  // 1.entry:入口起点//指示webpack以哪个文件为入口起点开始打包，分析构建内部依赖图
  entry: './src/index.js',
  // 2.output:输出//指示webpack打包后的资源bundles输出到哪里去，以及如何命名
  output: {
    // 输出文件名
    filename: 'built.js',
    // 输出路径
    // __dirname nodejs的变量，代表当前文件的目录绝对路径:(/03.打包样式资源)
    path: resolve(__dirname, 'build')
  },
  // loader的配置
  // loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）
  // 所以要处理css,image这样的文件，就需要使用loader
  module: {
      
  },
  // plugins的配置
  // loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。
  // 插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。
  plugins: [
    // 详细plugins的配置
  ],
  // 模式//指示webpack使用相应模式的配置
  mode: 'development', // 开发模式    //production生产模式
}
```

#### 1.1.常见的loader有哪些？如何配置？

> **webpack 默认支持处理 JS 与 JSON 文件，其他类型都处理不了，这里必须借助 Loader 来对不同类型的文件的进行处理。**

```js
module.exports = {
  //...
  // loader的配置
  module: {
    rules: [
      // 详细loader配置
      // 不同文件必须配置不同loader处理
      {
        // 匹配哪些文件
        test: /\.css$/,       //匹配所有的 css 文件
        // 使用哪些loader进行处理
        use: [
          // use数组中loader执行顺序：从右到左，从下到上 依次执行
          // 创建style标签，将js中的样式资源插入进行，添加到head中生效 //把 CSS 代码注入到 JavaScript 中
          'style-loader',
          // 将css文件变成commonjs模块加载js中，里面内容是样式字符串//使得webpack能够编译css
          'css-loader'   
        ]
      },
      {
        test: /\.less$/,   //匹配所有的 less 文件
        // less-loader将less文件编译成css文件
        // 需要下载 less-loader和less
        use: ['style-loader','css-loader','less-loader']
      },
        
       //file-loader把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件 (处理图片和字体) 
       // 打包其他资源(除了html/js/css资源以外的资源)
      {
        // 排除css/js/html资源
        exclude: /\.(css|js|html|less)$/,
        use:[
            loader: 'file-loader',  
            options: {
              name: '[hash:10].[ext]'  //进行重命名 [hash:10]取文件hash的前10位,[ext]取文件原来扩展名
            }
    	]
      },  
    
      // url-loader处理图片的，但是处理不了html中的img图片
     // 与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)  
      {
        // 问题：默认处理不了html中img图片
        // 处理图片资源
        test: /\.(jpg|png|gif)$/,
        // 使用一个loader
        // 下载 url-loader file-loader
        use:[
            loader: 'url-loader',
            options: {
              //图片大小小于8kb，就会被base64处理，优点: 减少请求数量（减轻服务器压力），缺点：图片体积会更大（文件请求速度更慢）
              limit: 8 * 1024,
              // 问题：因为url-loader默认使用es6模块化解析，而html-loader引入图片是commonjs
              // 解析时会出问题：[object Module]
              // 解决：关闭url-loader的es6模块化，使用commonjs解析
              esModule: false,
              //进行重命名 [hash:10]取文件hash的前10位,[ext]取文件原来扩展名
              name: '[hash:10].[ext]'
            }
		]
      },       
        
      /*
        js兼容性处理：babel-loader @babel/core 
          1. 基本js兼容性处理 --> @babel/preset-env
            问题：只能转换基本语法，如promise高级语法不能转换
          2. 全部js兼容性处理 --> @babel/polyfill  //直接在文件中引入
            问题：我只要解决部分兼容性问题，但是将所有兼容性代码全部引入，体积太大了~
          3. 需要做兼容性处理的就做：按需加载  --> core-js
      */  
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设：指示babel做怎么样的兼容性处理
          presets: [
            [
              '@babel/preset-env',
              {
            	// useBuiltIns: false 默认值，无视浏览器兼容配置，引入所有 polyfill
           	 	// useBuiltIns: entry 根据配置的浏览器兼容，引入浏览器不兼容的 polyfill
            	// useBuiltIns: usage 会根据配置的浏览器兼容，以及你代码中用到的 API 来进行 polyfill，实现了按需添加   
                useBuiltIns: 'usage',
                // 指定core-js版本
                corejs: {
                  version: 3
                },
                // 指定兼容性做到哪个版本浏览器
                targets: {
                  chrome: '60',
                  firefox: '60',
                  ie: '9',
                  safari: '10',
                  edge: '17'
                }
              }
            ]
          ]//presets
        }
      }
     
    ]
  },
  //...
}
```

#### 1.2.常用插件（Plugin）有哪些？如何的配置？

> 与 Loader 用于转换特定类型的文件不同，**插件（Plugin）可以贯穿 Webpack 打包的生命周期，执行不同的任务**
>
> 想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建它的一个实例。

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')

module.exports = {
  //....
  plugins:[ // 配置插件
    // html-webpack-plugin:将打包后的文件自动引入到html
    // 功能：默认会创建一个空的HTML，自动引入打包输出的所有资源（JS/CSS）
    // 需求：需要有结构的HTML文件  
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    //自动清空打包目录
    //每次打包的时候，打包目录都会遗留上次打包的文件，为了保持打包目录的纯净，我们需要在打包前将打包目录清空  
    new CleanWebpackPlugin(), // 引入插件
    //mini-css-extract-plugin: 分离样式文件，CSS 提取为独立文件，支持按需加载 
    //使用MiniCssExtractPlugin.loader, 这个loader取代style-loader。作用：提取js中的css成单独文件 
    new MiniCssExtractPlugin({
      // 对输出的css文件进行重命名
      filename: 'css/built.css'
    }),
    // 压缩css
    new OptimizeCssAssetsWebpackPlugin()
  ],
}

```

#### 1.3.区分环境

>**本地环境：**
>
>- 需要更快的构建速度
>- 需要打印 debug 信息
>- 需要 live reload 或 hot reload 功能
>- 需要 sourcemap 方便定位问题
>- ...
>
>**生产环境：**
>
>- 需要更小的包体积，代码压缩+tree-shaking
>- 需要进行代码分割
>- 需要压缩图片体积
>- ...

```js
//1.安装 cross-env
npm install cross-env -D

//2.配置启动命令
//打开 ./package.json
"scripts": {
    "dev": "cross-env NODE_ENV=dev webpack serve --mode development", 
    "test": "cross-env NODE_ENV=test webpack --mode production",
    "build": "cross-env NODE_ENV=prod webpack --mode production"
},
    
//3.在 Webpack 配置文件中获取环境变量
if (process.env.NODE_ENV === 'production') {
    //做生产环境的配置
}

module.exports = (env, argv) => {
  // 这里可以通过不同的模式修改 config 配置
  console.log('argv.mode=',argv.mode) // 打印 mode(模式) 值
  // 这里可以通过不同的模式修改 config 配置
  return config;
}
```

#### 1.4.启动 devServer

>  开发服务器 devServer：用来自动化（自动编译，自动打开浏览器，自动刷新浏览器~~）
>
>  特点：只会在内存中编译打包，不会有任何输出
>
>  启动devServer指令为：npx webpack-dev-server
>
> 注意：例子使用的`webpack-dev-server` 版本是 `3.11.2`，当版本 `version >= 4.0.0` 时，需要使用 [devServer.static](https://link.juejin.cn/?target=https%3A%2F%2Fwebpack.docschina.org%2Fconfiguration%2Fdev-server%2F%23devserverstatic) 进行配置，不再有 `devServer.contentBase` 配置项。

```js
module.exports = {
    //...
    //devServer与entry等是同级配置项
    devServer: {
    // 项目构建后路径
    contentBase: resolve(__dirname, 'build'),
    // 启动gzip压缩
    compress: true,
    // 端口号
    port: 9000,
    // 自动打开浏览器
    open: true
  }
}
```

### 2.优化配置

> 1.开发环境性能优化
>
> 1.1.优化打包构建速度:HMR
>
> 1.2.优化代码调试:source-map
>
> 2.生产环境性能优化
>
> 2.1.优化打包构建速度
>
> * oneOf
> * babel缓存
> * 多进程打包
> * externals
> * dll
>
> 2.2.优化代码运行的性能
>
> * 缓存(hash-chunkhash-contenthash)
> * tree shaking   
>   去除无用代码
>   前提：1. 必须使用ES6模块化  2. 开启production环境
>   作用: 减少代码体积
> * code split
> * 懒加载/预加载
> * pwa

#### 2.1.HMR

>  HMR: hot module replacement 热模块替换 / 模块热替换
>
>   作用：一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块）   极大提升构建速度
>
>    样式文件：可以使用HMR功能：因为style-loader内部实现了~
>
>    js文件：默认不能使用HMR功能 --> 需要修改js代码，添加支持HMR功能的代码
>
> ​    注意：HMR功能对js的处理，只能处理非入口js文件的其他文件。
>
>    html文件: 默认不能使用HMR功能.同时会导致问题：html文件不能热更新了~ （不用做HMR功能）
>
> ​    解决：修改entry入口，将html文件引入

#### 2.2.source-map

>一种 提供源代码到构建后代码映射 技术 （如果构建后代码出错了，通过映射可以追踪源代码错误）
>
>```js
>//除了 source-map 这种类型之外，还有很多种类型可以用，例如：
>eval
>eval-source-map
>cheap-source-map
>inline-source-map
>cheap-module-source-map
>inline-cheap-source-map
>cheap-module-eval-source-map
>inline-cheap-module-source-map
>hidden-source-map
>nosources-source-map
>```
>
>  source-map：外部 | 错误代码准确信息 和 源代码的错误位置
>
>  inline-source-map：内联 | 只生成一个内联source-map| 错误代码准确信息 和 源代码的错误位置
>
>  hidden-source-map：外部 |错误代码错误原因，但是没有错误位置  | 不能追踪源代码错误，只能提示到构建后代码的错误位置
>
>  eval-source-map：内联 |每一个文件都生成对应的source-map，都在eval  |错误代码准确信息 和 源代码的错误位置
>
>  nosources-source-map：外部| 错误代码准确信息, 但是没有任何源代码信息
>
>  cheap-source-map：外部 | 错误代码准确信息 和 源代码的错误位置 | 只能精确的行
>
>  cheap-module-source-map：外部 |错误代码准确信息 和 源代码的错误位置  |  module会将loader的source map加入
>
>  内联 和 外部的区别：1. 外部生成了文件，内联没有 2. 内联构建速度更快
>
>  开发环境：速度快，调试更友好
>
>   速度快(eval>inline>cheap>...)
>
>​    eval-cheap-souce-map
>
>​    eval-source-map
>
>   调试更友好  
>
>​    souce-map
>
>​    cheap-module-souce-map
>
>​    cheap-souce-map
>
>   -->推荐： eval-source-map  / eval-cheap-module-souce-map
>
>  生产环境：源代码要不要隐藏? 调试要不要更友好
>
>   内联会让代码体积变大，所以在生产环境不用内联
>
>   nosources-source-map 全部隐藏
>
>   hidden-source-map 只隐藏源代码，会提示构建后代码错误信息
>
>   -->推荐： source-map / cheap-module-souce-map

```js
//配置项
module.exports = {
    //...
    //devServer与entry等是同级配置项
    devtool: 'source-map'
}
```

#### 2.3.缓存

> ##### 缓存：
>
> 1. babel缓存
>
> * 配置项：cacheDirectory: true //--> 让第二次打包构建速度更快
>
> 2. 文件资源缓存
>
> * hash: 每次wepack构建时会生成一个唯一的hash值。
>
> ​    问题: 因为js和css同时使用一个hash值。
>
> ​     如果重新打包，会导致所有缓存失效。（可能我却只改动一个文件）
>
> * chunkhash：根据chunk生成的hash值。如果打包来源于同一个chunk，那么hash值就一样
>
> ​    问题: js和css的hash值还是一样的
>
> ​     因为css是在js中被引入的，所以同属于一个chunk
>
> * contenthash: 根据文件的内容生成hash值。不同文件hash值一定不一样   
>
>    --> 让代码上线运行缓存更好使用

#### 2.4.tree shaking：去除无用代码

>  前提：1. 必须使用ES6模块化  2. 开启production环境
>
>  作用: 减少代码体积
>
>1.   在package.json中配置 
>
>   "sideEffects": false 所有代码都没有副作用（都可以进行tree shaking）
>
>​    问题：可能会把css / @babel/polyfill （副作用）文件干掉
>
>   "sideEffects": ["*.css", "*.less"]
>
>2. Tree-shaking 作用是剔除没有使用的代码，以降低包的体积
>
>- webpack 默认支持，需要在 .bablerc 里面设置 `model：false`，即可在生产环境下默认开启

### 3.面试题

#### 3.1.说一说Loader和Plugin的区别？

> * Loader 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。
>   因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。
>
> * Plugin 就是插件，基于事件流框架 Tapable，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。
>
> * Loader 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。
>
> * Plugin 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

#### 3.2.Webpack构建流程简单说一下

> Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：
>
> - `初始化参数`：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数
> - `开始编译`：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
> - `确定入口`：根据配置中的 entry 找出所有的入口文件
> - `编译模块`：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
> - `完成模块编译`：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
> - `输出资源`：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
> - `输出完成`：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统
>
> 在以上过程中，`Webpack` 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。
>
> 简单说
>
> - 初始化：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
> - 编译：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
> - 输出：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

#### 3.3.source map是什么？生产环境怎么用？

>`source map` 是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 soucre map。
>
>map文件只要不打开开发者工具，浏览器是不会加载的。
>
>线上环境一般有三种处理方案：
>
>- `hidden-source-map`：借助第三方错误监控平台 Sentry 使用
>- `nosources-source-map`：只会显示具体行数以及查看源代码的错误栈。安全性比 sourcemap 高
>- `sourcemap`：通过 nginx 设置将 .map 文件只对白名单开放(公司内网)
>
>注意：避免在生产中使用 `inline-` 和 `eval-`，因为它们会增加 bundle 体积大小，并降低整体性能。

#### 3.4.模块打包原理知道吗？

> Webpack 实际上为每个模块创造了一个可以导出和导入的环境，本质上并没有修改 代码的执行逻辑，代码执行顺序与模块加载顺序也完全一致。

#### 3.5.说一下 Webpack 的热更新原理吧

> `Webpack` 的热更新又称热替换（`Hot Module Replacement`），缩写为 `HMR`。 这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。
>
> HMR的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个 `Websocket`，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发起 `Ajax` 请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 `jsonp` 请求获取该chunk的增量更新。
>
> 后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 `HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像`react-hot-loader` 和 `vue-loader` 都是借助这些 API 实现 HMR。

#### 3.6.webpack 做过哪些优化，开发效率方面、打包策略方面等等

> **1）优化 Webpack 的构建速度**
>
> - 使用高版本的 Webpack （使用webpack4）
> - 多线程/多实例构建：HappyPack(不维护了)、thread-loader
> - 缩小打包作用域：
>   - exclude/include (确定 loader 规则范围)
>   - resolve.modules 指明第三方模块的绝对路径 (减少不必要的查找)
>   - resolve.extensions 尽可能减少后缀尝试的可能性
>   - noParse 对完全不需要解析的库进行忽略 (不去解析但仍会打包到 bundle 中，注意被忽略掉的文件里不应该包含 import、require、define 等模块化语句)
>   - IgnorePlugin (完全排除模块)
>   - 合理使用alias
> - 充分利用缓存提升二次构建速度：
>   - babel-loader 开启缓存
>   - terser-webpack-plugin 开启缓存
>   - 使用 cache-loader 或者 hard-source-webpack-plugin
>      注意：thread-loader 和 cache-loader 兩個要一起使用的話，請先放 cache-loader 接著是 thread-loader 最後才是 heavy-loader
> - DLL：
>   - 使用 DllPlugin 进行分包，使用 DllReferencePlugin(索引链接) 对 manifest.json 引用，让一些基本不会改动的代码先打包成静态资源，避免反复编译浪费时间。
>
> 2）使用webpack4-优化原因
>
> - (a)V8带来的优化（for of替代forEach、Map和Set替代Object、includes替代indexOf）
> - (b)默认使用更快的md4 hash算法
> - (c)webpacks AST可以直接从loader传递给AST，减少解析时间
> - (d)使用字符串方法替代正则表达式
>
> ①noParse
>
> - 不去解析某个库内部的依赖关系
> - 比如jquery 这个库是独立的， 则不去解析这个库内部依赖的其他的东西
> - 在独立库的时候可以使用
>
> ```js
> module.exports = {
>   module: {
>     noParse: /jquery/,
>     rules:[]
>   }
> }
> ```
>
> ②IgnorePlugin
>
> - 忽略掉某些内容 不去解析依赖库内部引用的某些内容
> - 从moment中引用 ./locol 则忽略掉
> - 如果要用local的话 则必须在项目中必须手动引入
>
> ```js
> import 'moment/locale/zh-cn'
> module.exports = {
>     plugins: [
>         new Webpack.IgnorePlugin(/./local/, /moment/),
>     ]
> }
> ```
>
> ③dillPlugin
>
> - 不会多次打包， 优化打包时间
> - 先把依赖的不变的库打包
> - 生成 manifest.json文件
> - 然后在webpack.config中引入
> - webpack.DllPlugin Webpack.DllReferencePlugin
>
> ④happypack -> thread-loader
>
> - 大项目的时候开启多线程打包
> - 影响前端发布速度的有两个方面，一个是构建，一个就是压缩，把这两个东西优化起来，可以减少很多发布的时间。
>
> ⑤thread-loader
>  thread-loader 会将您的 loader 放置在一个 worker 池里面运行，以达到多线程构建。
>  把这个 loader 放置在其他 loader 之前（如下图 example 的位置）， 放置在这个 loader 之后的 loader 就会在一个单独的 worker 池(worker pool)中运行。
>
> ```js
> // webpack.config.js
> module.exports = {
>   module: {
>     rules: [
>       {
>         test: /.js$/,
>         include: path.resolve("src"),
>         use: [
>           "thread-loader",
>           // 你的高开销的loader放置在此 (e.g babel-loader)
>         ]
>       }
>     ]
>   }
> }
> 
> ```
>
> 每个 worker 都是一个单独的有 600ms 限制的 node.js 进程。同时跨进程的数据交换也会被限制。请在高开销的loader中使用，否则效果不佳
>
> ⑥压缩加速——开启多线程压缩
>
> - 不推荐使用 webpack-paralle-uglify-plugin，项目基本处于没人维护的阶段，issue 没人处理，pr没人合并。
>    Webpack 4.0以前：uglifyjs-webpack-plugin，parallel参数
>
> ```js
> module.exports = {
>   optimization: {
>     minimizer: [
>       new UglifyJsPlugin({
>         parallel: true,
>       }),
>     ],
>   },};
> ```
>
> - 推荐使用 terser-webpack-plugin
>
> ```js
> module.exports = {
>   optimization: {
>     minimizer: [new TerserPlugin(
>       parallel: true   // 多线程
>     )],
>   },
> };
> ```
>
> **2）优化 Webpack 的打包体积**
>
> - 压缩代码
> - 提取页面公共资源：
> - Tree shaking
> - Scope hoisting
> - 图片压缩
> - 动态Polyfill
>
> **3）speed-measure-webpack-plugin**
>  简称 SMP，分析出 Webpack 打包过程中 Loader 和 Plugin 的耗时，有助于找到构建过程中的性能瓶颈。 **开发阶段**
>
> 开启多核压缩 插件：** terser-webpack-plugin **
>
> ```js
> const TerserPlugin = require('terser-webpack-plugin')
> module.exports = {
>     optimization: {
>         minimizer: [
>             new TerserPlugin({
>                 parallel: true,
>                 terserOptions: {
>                     ecma: 6,
>                 },
>             }),
>         ]
>     }
> }
> ```

### 4.Bable

> ### 简单描述一下 Babel 的编译过程
>
> Babel 是一个 JavaScript 编译器，是一个工具链，主要用于将采用 ECMAScript 2015+ 语法编写的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。
>
> `Babel` 本质上就是在操作 `AST` 来完成代码的转译。AST是抽象语法树（Abstract Syntax Tree, AST）
>
> 如果想要了解更多，可以阅读和尝试：
>
> - 分析 `AST`：[ASTexplorer.net](https://link.juejin.cn?target=https%3A%2F%2Fastexplorer.net%2F)
> - `AST` 规范：[github.com/estree/estr…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Festree%2Festree)
>
> `Babel` 的功能很纯粹，它只是一个编译器。大多数编译器的工作过程可以分为三部分：
>
> 1. **解析（Parse）** ：将源代码转换成更加抽象的表示方法（例如抽象语法树）。包括词法分析和语法分析。词法分析主要把字符流源代码（Char Stream）转换成令牌流（ Token Stream），语法分析主要是将令牌流转换成抽象语法树（Abstract Syntax Tree，AST）。
> 2. **转换（Transform）** ：通过 Babel 的插件能力，对（抽象语法树）做一些特殊处理，将高版本语法的 AST 转换成支持低版本语法的 AST。让它符合编译器的期望，当然在此过程中也可以对 AST 的 Node 节点进行优化操作，比如添加、更新以及移除节点等。
> 3. **生成（Generate）** ：将 AST 转换成字符串形式的低版本代码，同时也能创建 Source Map 映射。
>
> 经过这三个阶段，代码就被 `Babel` 转译成功了。
>
> ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a30bf2739fec4c29847ba1675c03b62f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c73423b335c34399b4e69b61515365ad~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
