# 极客时间-玩转webpack-学到了34章（未完待续..
## 为什么需要构建工具
+ 转换es6
+ 转换jsx
+ css前缀补全/预处理器
+ 压缩混淆
+ 图片压缩

## 构建演变之路
1. ant、YUITool
2. grunt
3. fis3、gulp
4. rollup、webpack、parcel

## 为什么选择webpack
1. 社区生态丰富
2. 配置灵活
3. 官方迭代更新快

## webpack配置文件
webpack默认配置文件：webpack.config.js
可以通过webpack --config指定配置文件

## 安装webpack
1. 创建空目录
2. npm init -y (默认都选择yes)
3. npm install webpack webpack-cli --save-dev

## 编写第一个简单的应用
```js
//src文件夹内helloworld.js
export function helloworld(){
  return 'hello webpack'
}
//src文件夹内index.js
import {helloworld} from "./helloworld"
document.write(helloworld());
//根目录下webpack.config.js
"use strict";

const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.join(__dirname, "dist"),
    filename: "bundle.js"
  },
  mode: "production"
};

```
## bug1

```
Invalid configuration object. Webpack has been initialised using a configuration object that does not match the API schema.
 - configuration.entry should be an non-empty string.
 
//原因是因为我的entry填的是空
```
## 在package.json中添加执行语句
```js
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  
//添加自动化语句，webpack打包
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build":"webpack"
  },
```
## entry
entry是打包的入口
> 单入口
entry是一个字符串
> 多入口
entry是一个对象

## output
用来告诉webpack如何将编译后的文件输出到磁盘
> 单入口配置

```js
output:{
    filename:'[name].js',
    path:__dirname+'/dist'
}
```
### 例子
```js
//src文件夹下的search.js
document.write('search page');

//根目录下的webpack.config.js
"use strict";

const path = require("path");

module.exports = {
  entry: {
    index: "./src/index.js",
    search: "./src/search.js"
  },
  output: {
    path: path.join(__dirname, "dist"),
    filename: "[name].js"
  },
  mode: "production"
};
```
打包出来后，dist文件夹下有两个js文件

## loaders
通过loaders去支持其他文件类型并且把他们转化成有效的模块，并且可以添加到依赖图中
```js
module.export={
 module:{
    rules:[
    {test:/\.txt$/,use:'raw-loader'}
    ]
}   
}

//test指定匹配规则 use指定使用的loader名称
```
## plugins
插件用于bundle文件的优化，资源管理和环境变量注入
作用于整个构建过程
> commonsChunkPlugin 将chunks相同的模块代码提取成公共js
> cleanwebpackplugin 清理构建目录

```js
module.export={
plugins:[new HtmlWebpackPlugin({template:'./src/index.html'})]
}
//放到plugins数组里
```
## Mode
production     development    none
设置mode可以使用webpack内置的函数，默认值为production 

## 解析es6
使用babel-loader
babel的配置文件是 .babelrc
```js

//webpack.config.js
module.export={
 module:{
    rules:[
    {test:/\.js$/,use:'babel-loader'}
    ]
}   
}
// .babelrc
{
    'presets':[
    '@babel/preset-env'
    ],
    'plugins':[
        '@babel/proposal-class-properties'
    ]
}
```

根目录安装babel es6解析器
npm i @babel/core @babel/preset-env babel-loader -D

根目录下新建 .babelrc文件
```js
{
  "presets":[
    "@babel/preset-env"
  ]
}
```
修改 webpack.config.js
```js
module.exports = {
     module: {
    rules: [{ test: /.js$/, use: "babel-loader" }]
  }
}
```
## 增加对react项目的支持
npm i react react-dom @babel/preset-react -D
修改.babelrc文件，添加对于react的支持
```js
{
  "presets":[
    "@babel/preset-env",
    "@babel/preset-react"
  ]
}
```

## webpack 里面解析css
css-loader用于加载 .css文件，并且转换成commonjs对象
style-loader将样式通过 <style>标签插入到head中

npm i style-loader css-loader -D

//配置webpack.config.js

```js
  module: {
    rules: [
      { test: /.js$/, use: "babel-loader" },
      { test: /.css$/, use: ["style-loader", "css-loader"] }
    ]
  }
//style-loader需要在css-loader之前，因为会进行链式调用
```

## webpack 配置less sass
npm i less less-loader -D

```js
module.exports={
    module: {
    rules: [
      { test: /.js$/, use: "babel-loader" },
      { test: /.css$/, use: ["style-loader", "css-loader"] },
      {test:/.less$/,use:["style-loader", "css-loader",'less-loader']}
    ]
  }
}
```

## webpack解析图片
file-loader 用于处理文件
```js
{
    test:/.(png|jpg|gif|jpeg)$/,
    use:'file-loader'
}
```
同时还能用于解析字体
```js
{
    test:/.(woff|woff2|eot|ttf|otf)$/,
    use:'file-loader'
}
```

url-loader也可以处理图片和字体，可以设置较小资源自动base64
```js
{
    test:/.(png|jpg|gif|jpeg)$/,
    use:[
    {
    loader:'url-loader',
    options:{
        limit:10240 //字节
    }
}]
}
```
## webpack中的文件监听
> 轮询判断文件的最后编辑时间是否变化，某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等aggregateTimeout

两种方式：
启动webpack命令时，带上 --watch 参数
缺点：每次需要手动刷新浏览器

在配置webpack.config.js中设置watch：true
```js
module.exports = {
 watch:true,
 watchOptions:{
     ignored:/node_modules/,
     aggregateTimeout:300,
     poll:1000
 }
};
```
## webpack 比监控更好的热更新
webpack-dev-server （这个插件，你得安装
wds 不刷新浏览器
wds不输出文件，而是放在内存中
使用HotModuleReplacementPlugins插件
```js
//package.json

"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "dev":"webpack-dev-server --open"
  },
  
//webpack.config.js

const webpack=require("webpack");

module.exports = {
    plugins:[
        new webpack.HotModuleReplacementPlugin()
    ],
    devServer:{
        contentBase:"./dist",
        hot:true
    }
}
```

第二种热更新插件
webpack-dev-middleware
wdm将webpack输出的文件传输给服务器
适用于灵活的定制场景

## 文件指纹
打包后输出的文件名的后缀
hash 和整个项目的构建有关，只要项目文件有修改，整个项目构建的hash值就会变

chunkhash 和webpack打包的chunk有关，不同的entry会生成不同的chunkhash值

contenthash 根据文件内容来定义hash 文件内容不变，则contenthash不变

1. js设置output的filename，使用[chunkhash]
```js
module.exports = {
     output: {
    path: path.join(__dirname, "dist"),
    filename: "[name][chunkhash:8].js"
  },
}
```
2. css的文件设置MiniCssExtractPlugin的filename，使用[contenthash]
npm i mini-css-extract-plugin -D

```js
const MiniCssExtractPlugin=require("mini-css-extract-plugin")
module.exports = {
   plugins:[ new MiniCssExtractPlugin({
    filename:`[name][contenthash:8].css`
})]
}
```
3. 图片的文件指纹设置，设置file-loader的name，使用[hash]
```js
module.exports = {
    module: {
    rules: [
     {test:/\.(png|svg|jpg|gif)$/},
     use:[{
         loader:'file-loader',
         options:{
         name:'img/[name][hash:8].[ext]'
     }
     }]
    ]
  },
}
```

## webpack做代码压缩
html压缩 css压缩 js压缩
1. js压缩 内置了uglifyjs-webpack-plugin
2. css压缩 使用了optimize-css-assets-webpack-plugin 同时使用cssnano
```js
module.exports = {
 plugins:[new OptimizeCSSAssetsPlugin({
    assetNameRegExp:/\.css$/g,
    cssProcessor:require('cssnano')
})]
}
```
3. html压缩 html-webpack-plugin，设置压缩参数
几个页面，调用几次HtmlWebpackPlugin
```js
module.exports = {
 plugins:[new HtmlWebpackPlugin({
    template:path.join(__dirname,'src/search.html'),
    filename:'search.html',
    chunks:['search']，
    inject:true,
    minify:{
    html:true,
    collapseWhitespace:true,
    preserveLineBreaks:false,
    minifyCSS:true,
    minifyJS:true,
    removeComments:false
}
})]
}
```
## 自动清理构建目录，打包生成文件不再增加
避免构建每次都需要手动删除dist
使用clean-webpack-plugin（默认会删除output指定的输出目录
```js
module.exports = {
 plugins:[new CleanWebpackPlugin()]
}
```

## webpack增强css功能 prefixer
使用autoprefixer插件自动补齐css3前缀
npm i postcss-loader autoprefixer -D
```js
module: {
    rules: [
      { test: /.js$/, use: "babel-loader" },
      { test: /.css$/, use: ["style-loader", "css-loader"] },
      { test: /.less$/, use: [MiniCssExtractPlugin.loader,'css-loader','less-loader',{
          loader:'postcss-loader',
          options:{
          plugins:()=>{
          require('autoprefixer')({
          browsers:['last 2 version','>1%','ios 7'] //浏览器最近的两个版本，大于1%以上的人使用的，ios7以上的人使用
      })
      }
      }
      }] }
    ]
  },
```
## webpack增强css 转换rem
以前使用css媒体查询实现响应式布局，需要编写多套适配样式代码    
px2rem-loader插件    

页面渲染时计算根元素的font-size值    
可以用手淘的lib-fiexible库  
```js
module: {
    rules: [
      { test: /.js$/, use: "babel-loader" },
      { test: /.css$/, use: ["style-loader", "css-loader"] },
      { test: /.less$/, use: [MiniCssExtractPlugin.loader,'css-loader','less-loader',{
          loader:'postcss-loader',
          options:{
          plugins:()=>{
          require('autoprefixer')({
          browsers:['last 2 version','>1%','ios 7'] //浏览器最近的两个版本，大于1%以上的人使用的，ios7以上的人使用
      })
      }
      }
      },{
          loader:'px2red-loader',
          options:{
          remUnit:75, //一个rem等于75px
          remPrecision:8 //px转化成rem 小数点后面的位数
      }
      }] }
    ]
  },
```
在生成的html文件中，自己写上script标签，内联进去，复制库中代码进去

## 资源内联
代码层面：
页面框架的初始化脚本
上报相关打点
css内联避免页面闪动

请求层面：减少http网络请求数
小图片火车字体内联

1. html和js内联 可以使用raw-loader
2. css内联：
 i. 借助style-loader
 ii. html-inline-css-webpack-plugin
 
## 多页面应用打包方案
1. 每个页面对应一个entry，一个html-webpack-plugin
缺点： 每次新增或删除页面需要更改webpack配置
2. 动态获取entry和设置html-webpack-plugin数量，利用glob.sync
```js
entry:glob.sync(path.join(__dirname,"./src/*/index.js"));
```
i. npm i glob -D
ii.
```js
//webpack.prod.js

const glob=require('glob');
const setMPA=()=>{
    const entry={};
    const htmlWebpackPlugins=[];
    const entryFiles=glob.sync(path.join(__dirname,"./src/*/index.js"))
    Object.keys(entryFiles).map((index)=>{
        const entryFile=entryFiles[index];
        const match=entryFile.match(/src\(.*)\/index\.js/);
        const pageName=match&&match[1];
        
        entry[pageName]=entryFile;
        
        htmlWebpackPlugins.push({
            template:path.join(__dirname,`src/${pageName}/index.html`),
    filename:`${pageName}.html`,
    chunks:[pageName]，
    inject:true,
    minify:{
    html:true,
    collapseWhitespace:true,
    preserveLineBreaks:false,
    minifyCSS:true,
    minifyJS:true,
    removeComments:false
}

    })
    })
    return{entry,htmlWebpackPlugins}
}
const {entry,htmlWebpackPlugins}=setMPA();

module.export={
    entry:entry,
    plugins:[].concat(htmlWebpackPlugins)
}
```

## 使用sourcemap

作用通过source map定位到源代码

开发环境开启，线上环境关闭
    线上排查问题的时候可以讲sourcemap上传到错误监控系统
    
source map 关键字
i. eval使用eval包裹模块代码
ii. source map：产生.map文件
iii. cheap：不包含列信息
iiii. 将map作为Dateurl
```js
module.exports = {
    devtool:'source-map'
}
```

## webpack提取公共资源(28 没看懂)
思路： 将react、react-dom基础包通过cdn打入，不打入bundle中
方法：+ 使用html-webpack-externals-plugin
+ 使用webpack4内置的splitchunksplugin，代替CommonsChunkPlugin插件
    chunks参数说明：async 异步引入的库进行分离
                    initial 同步引入的库进行分离
                    all 所有引入的库进行分离（推荐）

module.exports={
    new HtmlWebpackPlugin({
    chunks:['verdors',pageName]
})
    optimization:{
    splitChunks:{
    minSize:0, //分离的包体积的大小
    cacheGroups:{
    commons:{
    test:/(react|react-dom)/,   //匹配规则
    name:'vendors'，
    chunks:'all',
    minChunks:2, //设置最小引用次数为2次
}
}
}
}
}

## webpack tree shaking（摇树优化）
1个模块可能有多个方法，tree shaking就是只把用到的方法打入bundle，没用到的方法，uglify阶段被擦除掉

使用： webpack默认支持，在.babelrc里设置modules:false 即可
        production mode的情况下默认开启

要求： 必须是es6的语法，cjs的方式不支持

原理：
    利用es6模块的特点
    只能作为模块顶层的语句出现
    import的模块名只能是字符串常量
    import binding是immutable的
    
    
## scopeHoisting使用原理
大量函数闭包包裹代码，导致体积增大（模块越多越明显
运行代码时创建的函数作用域变多，内存开销变大

原理：将所有模块的代码按照引用顺序放在一个函数作用域里，然后适当的重命名一些变量以防止变量名冲突

对比：scope hoisting 可以减少函数生命代码和内存开销

webpack mode为production 默认开启 必须是es6语法，cjs不支持

## 代码分割 (31
懒加载js脚本的方式
CommonJS：require.ensure
ES6：动态import（需要babel

npm install @babel/plugin-syntax-dynamic-import --save-dev

```js
//.babellrc

{
     "presets":[
    "@babel/preset-env",
    "@babel/preset-react"
  ],
  "plugins":[
    @babel/plugin-syntax-dynamic-import
  ]
}
```

## webpack和eslint结合
行业内优秀的ESLint规划
1. Airbnb：eslint-config-airbnb
2. 腾讯 alloyteam团队 eslint-config-alloy
3. ivweb团队 eslint-config-ivweb

不重复造轮子，基于eslint:recommend配置并改进

怎么落地：
和CI/CD系统集成
和webpack集成

本地开发阶段增加precommit钩子：
安装husky
npm install husky --save-dev

增加 npm script，通过lint-staged增量检查修改的文件
```js
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "dev": "webpack-dev-server --open",
    "precommit":"lint-staged"
  },
 "lint-staged":{
     "linters":{
     "*.{js,scss}":{"eslint--fix","git add"}
 }
 }
```

webpack与ESLint集成（32
使用eslint-loader,构建时检查JS规范
```js
module.exports={
    module:{
   rules:[{
    test:/\.js$/,
    exclude:/node_modules/,
    use:[
        "babel-loader","eslint-loader"
    ]
}] 
}
}

//根目录下创建eslint配置文件
//安装babel-eslint
//.eslintrc.js

module.exports={
    "parser":"babel-eslint",
    "extends":"airbnb",
    "rules":{
        "semi":"error"
}
}

```

## webpack打包一个组件或者一个库（33
打包一个大整数加法：
支持的使用方式

支持ES module
```js
import * as largeNumber from "large-number";
largeNumber.add("999",1);
```

支持CJS
```js
const largeNumbers=require("large-number");
largeNumbers.add("999",1);
```

支持AMD
require(['large-number']),function('large-number'){
    largeNumber.add('999',1)
}

编写好代码后，编写webpack.config.js
要一个压缩版，一个非压缩版
npm i terser-webpack-plugin -D
```js
const TerserPlugin=require("terser-webpack-plugin");
module.exports={
    entry:{
    'large-number':'./src/index.js',
    'large-number.min':"./src/index.js"
},
output:{
    filename:"[name].js",
    library:'largeNumber',
    libraryTarget:'umd'，
    libraryExport：'default'
}，
mode:"none",//默认不压缩
optimization:{
    minimize:true,
    minimizer:[
        new TerserPlugin({
    include:/\.min\.js$/
})
    ]
},
main:"index.js"
}

//根目录下新建index.js
if(process.env.NODE_ENV="production"){
    module.exports=require("./dist/large-number.min.js")
}else{
    module.exports=require("./dist/large-number.js")
}
```

## 服务端渲染SSR（34