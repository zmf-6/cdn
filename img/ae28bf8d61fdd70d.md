##### 优化webpack

- 缩⼩⽂件范围 **Loader**

```javascript
// test include exclude三个配置项来缩⼩loader的处理范围
// 推荐include
```

- 优化**resolve.modules**配置

```javascript
// resolve.modules⽤于配置webpack去哪些⽬录下寻找第三⽅模块，默认是['node_modules']
module.exports={
	resolve:{
		modules: [path.resolve(__dirname, "./node_modules")]
	} 
}
```

- 优化**resolve.alias**配置

```javascript
// resolve.alias配置通过别名来将原导⼊路径映射成⼀个新的导⼊路径
alias: {
"@": path.join(__dirname, "./pages"),
"react": path.resolve(
  __dirname,
  "./node_modules/react/umd/react.production.min.js"
 ),
"react-dom": path.resolve(
  __dirname,
  "./node_modules/react-dom/umd/react-dom.production.min.js"
 )
}
```

- 优化**resolve.extensions**配置

```javascript
extensions:['.js','.json','.jsx','.ts']
// 后缀尝试列表尽量的⼩
// 导⼊语句尽量的带上后缀
```

- 使⽤**externals**优化**cdn**静态资源

```javascript
// 将⼀些JS⽂件存储在 CDN 上(减少 Webpack 打包出来的 js 体积), index.html 中通过标签引⼊
// 如果我们还想使用import $ from 'jquery'等语句
externals: {
  //jquery通过script引⼊之后，全局中即有了 jQuery 变量
  'jquery': '$'
}
```

- 使⽤静态资源路径**publicPath(CDN)**

```javascript
// CDN通过将资源部署到世界各地，使得⽤户可以就近访问资源，加快访问速度。要接⼊CDN，需要把⽹⻚的静态资源上传到CDN服务上，在访问这些资源时，使⽤CDN服务提供的URL。
output:{
 	publicPath: '//cdnURL.com', //指定存放JS⽂件的CDN地址
}
// 注意要确保静态资源⽂件的上传与否
```

##### 区分开发环境和生产环境

```javascript
// webpack-merge 合并配置
// copy-webpack-plugin 拷贝静态资源
// optimize-css-assets-webpack-plugin 压缩css
// uglifyjs-webpack-plugin 压缩js

const { merge } = require('webpack-merge') // 进行合并
配置文件分为：
webpack.common.js
webpack.prod.js
webpack.dev.js

打包运行命令: 
npx webpack --config webpack.prod.js
服务器运行命令: 
npx webpack --config webpack.dev.js


// 另一种区分环境变量的措施
// npm i cross-env -D 
// 一下是cross-env的使用流程

// 在package.json中使用
"test": "cross-env NODE_ENV=production webpack --config ./webpack.config.test.js",
  
//外部传⼊的全局变量
const { merge } = require('webpack-merge')
const commonConfig = require('webpack.common.js')
const prodConfig = require('webpack.prod.js')
const devConfig = require('webpack.dev.js')

let env = process.env.NODE_ENV
module.exports = (env)=>{
  if(env){
    return merge(commonConfig,prodConfig)
  }else{
    return merge(commonConfig,devConfig)
  } 
}
```

##### Tree Shaking

```javascript
// 清除⽆⽤ css,js(Dead Code)
代码不会被执⾏，不可到达
代码执⾏的结果不会被⽤到
代码只会影响死变量（只写不读）
Js tree shaking只⽀持ES module的引⼊方式
```

- css的Tree shaking

```javascript
npm i glob-all purify-css purifycss-webpack --save-dev
const PurifyCSS = require('purifycss-webpack')
const glob = require('glob-all')
plugins:[
// 清除⽆⽤ css
new PurifyCSS({
		paths: glob.sync([
       // 要做 CSS Tree Shaking 的路径⽂件
       // 请注意，我们同样需要对 html ⽂件进⾏ tree shaking
      path.resolve(__dirname, './src/*.html'),
      path.resolve(__dirname, './src/*.js')
    ])
 })
]
```

- 副作用

```javascript
// package.json
// 正常对所有模块进⾏tree shaking , 仅⽣产模式有效，需要配合
"sideEffects":false 
// usedExports或者 在数组⾥⾯排除不需要tree shaking的模块
"sideEffects":['*.css','@babel/polyfill']
```

##### 代码分割 code Splitting

```javascript
// 将公共代码抽离出来 比如loadsh，react,react-dom,vue等
optimization: {
 splitChunks: {
 	chunks: "all", // 所有的 chunks 代码公共的部分分离出来成为⼀个单独的⽂件
 },
},

// 所有配置
splitChunks: {
  chunks: 'async',//对同步 initial，异步 async，所有的模块有效 all
  minSize: 30000,//最⼩尺⼨，当模块⼤于30kb
  maxSize: 0,//对模块进⾏⼆次分割时使⽤，不推荐使⽤
  minChunks: 1,//打包⽣成的chunk⽂件最少有⼏个chunk引⽤了这个模块
  maxAsyncRequests: 5,//最⼤异步请求数，默认5
  maxInitialRequests: 3,//最⼤初始化请求书，⼊⼝⽂件同步请求，默认3
  automaticNameDelimiter: '-',//打包分割符号
  name: true,//打包后的名称，除了布尔值，还可以接收⼀个函数function
  cacheGroups = {//缓存组
    vendors: {
      test: /[\\/]node_modules[\\/]/,
      name: "vendor", // 要缓存的 分隔出来的 chunk 名称
      priority: -10//缓存组优先级 数字越⼤，优先级越⾼
    },
    other: {
      // 必须三选⼀： "initial" | "all" | "async"(默认就是async)
      chunks: "initial",
      test: /react|lodash/, // 正则规则验证，如果符合就提取 chunk,
      name: "other",
      minSize: 30000,
      minChunks: 1,
    },
    default: {
      minChunks: 2,
      priority: -20,
      reuseExistingChunk: true//可设置是否重⽤该chunk
    }
  }
}
```

##### Scope Hoisting

```javascript
// 作⽤域提升（Scope Hoisting）是指 webpack 通过 ES6 语法的静态分析，分析出模块之间的依赖关系，尽可能地把模块放到同⼀个函数中。下⾯通过代码示例来理解：

// hello.js
export default 'Hello, Webpack';
// index.js
import str from './hello.js';
console.log(str);

// webpack.config.js
module.exports = {
  optimization: {
  	concatenateModules: true
  }
};

// 最终结果现hello.js内容和index.js的内容合并在⼀起。 Scope Hoisting 的功能可以让Webpack 打包出来的代码⽂件更⼩、运⾏的更快。
```

##### 使用工具量化 npm i speed-measure-webpack-plugin -D

```javascript
npm i speed-measure-webpack-plugin -D // 可以测量各个插件和 loader 所花费的时间
//webpack.config.js
const SpeedMeasurePlugin = require("speed-measure-webpack-plugin");
const smp = new SpeedMeasurePlugin();
const config = {
	//...webpack配置
}
module.exports = smp.wrap(config);
```

```javascript
npm install webpack-bundle-analyzer -D // 分析webpack打包后的模块依赖关系
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
module.exports = merge(baseWebpackConfig, {
	//....
  plugins: [
    //...
    new BundleAnalyzerPlugin(),
   ]
})
```

##### 动态链接库

```javascript
// 项⽬中引⼊了很多第三⽅库，这些库在很⻓的⼀段时间内，基本不会更新，打包的时候分开打包来提升打包速度，⽽DllPlugin动态链接库插件，其原理就是把⽹⻚依赖的基础模块抽离出来打包到dll⽂件中，当需要导⼊的模块存在于某个dll中时，这个模块不再被打包，⽽是去dll中获取。

// webpack已经内置了对动态链接库的⽀持
// 	DllPlugin:⽤于打包出⼀个个单独的动态链接库⽂件
// 	DllReferencePlugin：⽤于在主要的配置⽂件中引⼊DllPlugin插件打包好的动态链接库⽂件
```

##### 基本步骤

- 新建建webpack.dll.config.js

```javascript
const path = require("path");
const { DllPlugin } = require("webpack");
module.exports = {
  mode: "development",
  entry: {
    react: ["react", "react-dom"] //! node_modules?
  },
  output: {
    path: path.resolve(__dirname, "./dll"),
    filename: "[name].dll.js",
    library: "react"
  },
  plugins: [
    new DllPlugin({
      // manifest.json⽂件的输出位置
      path: path.join(__dirname, "./dll", "[name]-manifest.json"),
      // 定义打包的公共vendor⽂件对外暴露的函数名
      name: "react"
    })
  ]
}
```

- package.json中添加

```javascript
"dev:dll": "webpack --config ./build/webpack.dll.config.js",
```

- 运行

```javascript
npm run dev:dll

//你会发现多了⼀个dll⽂件夹，⾥边有dll.js⽂件，这样我们就把我们的React这些已经单独打包了dll⽂件包含了⼤量模块的代码，这些模块被存放在⼀个数组⾥。⽤数组的索引号为ID,通过变量讲⾃⼰暴露在全局中，就可以在window.xxx访问到其中的模块
// Manifest.json 描述了与其对应的dll.js包含了哪些模块，以及ID和路径。
```

- 在webpack.config.js中使用

```javascript
new DllReferencePlugin({
	manifest: path.resolve(__dirname,"./dll/react-manifest.json")
}),
```

- 在index.html中手动添加依赖

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta http-equiv="X-UA-Compatible" content="ie=edge" />
  <title>webpack</title>
  <link href="css/main_e2bf39.css" rel="stylesheet">
</head>
<body>
  <div id="app"></div>
  <script type="text/javascript" src="react.dll.js"></script>
  <script type="text/javascript" src="js/main_142e6c.js"></script>
</body>
</html>
```

- 也可以使用插件添加

```javascript
// npm i add-asset-html-webpack-plugin
new AddAssetHtmlWebpackPlugin({
  // 对应的 dll ⽂件路径
	filepath: path.resolve(__dirname, '../dll/react.dll.js') 
}),
```



##### webpack5中的动态连接库

```javascript
// 在Webpack5中已经不⽤dll了，⽽是⽤ HardSourceWebpackPlugin ，⼀样的优化效果，但是使⽤却及其简单

// 主要作用：
1、提供中间缓存的作⽤
2、⾸次构建没有太⼤的变化
3、第⼆次构建时间就会有较⼤的节省

const HardSourceWebpackPlugin = require('hard-source-webpack-plugin')
const plugins = [
	new HardSourceWebpackPlugin()
]
```

##### happypack并发执行任务

```javascript
// 它将任务分解给多个⼦进程去并发执⾏，⼦进程处理完后再将结果发送给主进程。从⽽发挥多核 CPU 电脑的威⼒。

// npm i - D happypack
var happyThreadPool = HappyPack.ThreadPool({ size: 5 });
//const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length })
// webpack.config.js
rules: [
  {
    test: /\.jsx?$/,
    exclude: /node_modules/,
    use: [
      {
        // ⼀个loader对应⼀个id
        loader: "happypack/loader?id=babel"
      }
    ]
  },
  {
    test: /\.css$/,
    include: path.resolve(__dirname, "./src"),
    use: ["happypack/loader?id=css"]
  },
]
//在plugins中增加
plugins: [
  new HappyPack({
    // ⽤唯⼀的标识符id，来代表当前的HappyPack是⽤来处理⼀类特定的⽂件
    id: 'babel',
    // 如何处理.js⽂件，⽤法和Loader配置中⼀样
    loaders: ['babel-loader?cacheDirectory'],
    threadPool: happyThreadPool,
  }),
  new HappyPack({
    id: "css",
    loaders: ["style-loader", "css-loader"]
  }),
]
```

