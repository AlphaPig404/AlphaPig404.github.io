---
tag: [webpack] title: webpack从入门到放弃
---
## Webpack安装和命令行
### 安装
	
	npm install webpack --save-dev安装到当前文件  
	或者npm install webpack -g 安装到全局
	
### 命令行
* 举个例子，现在有main.js文件需要打包

		// main.js
		function hello(str){
			alert(str);
		}

	直接在当前文件夹下运行命令
		
		webpack main.js bundle.js
		
	会将main.js打包成bundle.js

<!--more-->
* 在main.js中引入js文件
	
		// main.js
		require('./world.js');引入当前文件夹下的world.js文件
		 
		function hello(str){
			alert(str);
		}
	再次执行命令
		
		webpack main.js bundle.js
		
	webpack会将world.js和main.js一起打包成bundle.js,这就是webpack最基本的作用，通过模块间的依赖关系，将相关的模块文件打包在一起。webpack支持三种模块化规范，AMD/CMD，Commonjs和ES6的规范。会通过模块的引入来分析依赖关系，从而实现打包功能。
	
* 在main.js中引入css文件
	
		//main.js
		
		require('./main.css');引入当前文件夹下的main.css
		
		function hello(str){
			alert(str);
		}
	执行命令
		
		webpack main.js bundle.js
		
	会报错，因为缺少处理style.css的loader
	
	>webpack的loaders是一大特色，也是很重要的一部分。webpack就是借用loaders对各种类型的文件进行各种不同的处理来实现打包和构建功能。
	
	loader部分之后会详细说明，先来处理下这里的报错。
 	
 		有两种方式：
 		1. 在require文件的时候写上loader处理，
 		如  requrie('css-loader!./main.css');
 		这样webpack就能处理css文件了。这里仅仅是消除了报错，
 		如果需要让css文件起作用的话，还需要加上style-loader,
 		如  require('style-loader!css-loader!./main.css')
 		2. 在命令行中主动声明使用的loader
 		如 webpack main.js bundle.js --module-bind 'css=style-loader!css-loader'
 	如上面的--module-bind的命令，webpack还提供了很多的命令，直接输入webpack会显示命令列表。其中常用的有--watch，能监视文件的变化自动执行打包命令；--progress，显示打包过程；--display-modules,会将打包的模块全部列举出来；--dispaly-reasons,会列举出打包的原因...

## webpack的基本配置
实际项目中我们不可能记住记住那么多的命令去在命令行中执行，因此需要在webpack.config.js中去配置这些命令。因为当我们直接在命令行中输入webpack命令时，webpack会在项目文件目录下找到webpack.config.js文件来执行。
	
	// webpack.config.js
	module.exports = {
		entry: './src/script/main.js', // 指定入口文件位置
		output: {
			path: './dist/js',  // 指定打包后文件位置
			filename: 'bundle.js' // 指定打包后文件名称
		}
	}
然后直接执行webpack命令就可以完成打包的工作。也可以使用’webpack --config 文件名‘来指定webpack要执行的配置文件。然后配合npm自动的脚本工作script来绑定不同的命令。比如在package.json中的scripts中设置webpack为'webpack --config webpack.config.js --progress --disaplay-module',然后在命令行中执行npm run webpack就相当于直接在命令行中执行了后面那一长串的命令。Vulcli的npm run dev和npm run build就是利用了npm的这一特性。

### entry配置
* 单页面项目只有一个入口文件时，直接配置文件的入口
		
		entry:'./entry'
	
* 单页面项目有多个入口文件时，处理没有依赖关系的文件

		entry:['./entry1','./entry2']
		
* 多页面应用程序中，需要针对不同的页面配置不同的入口
	
		entry: {
			page1: './page1',
			page2: ['./entry1','./entry2']
		}
		
	这个时候如果不修改output配置的话，不同页面打包生产的bundle.js文件会进行覆盖，这显然不是我们需要的。因此需要对output进行配置

### output配置
* 单入口文件时，如之前的配置
	
		{
			entry: './src/app.js',
			output: {
				filename: 'bundle.js',
				path: './built'
			}
		}
 		// writes to disk: ./bulit/bundle.js
 		
* 多入口文件时，需要修改配置

		{
			entry: {
				app:'./src/app.js',
				search: './src/search.js'
			},
			output: {
				filename: '[name].js',
				path: __dirname + '/built'
			}
		}
		
		// writes to dist: ./built/app.js, ./built/search.js
	上面是使用了[name]的占位符来配置的，webpack提供了三种站位符：
	[name] => 使用chunk的名称来占位
	[hash] => 使用每次打包生产的hash值来占位
	[chunkhash] => 使用chunk文件的hash值来占位，相当于chunk文件的版本号

### plugins插件
插件能够帮助我们实现一些特定的功能。比如说html-webpack-plugin会将打包完成后的文件自动插入到配置的html文件中。
使用方法：
	
		npm install html-webpack-plugin --save-dev // 安装
		
		// 引入到webpack.config.js
		var htmlWebpackPlugin = require('html-webpack-plugin');
		
		module.exports = {
			// context: '/'; 文件默认路径都是从根目录开始
			entry: {
				app:'./src/app.js',
				search: './src/b.js',
				a: './src/a.js'
			},
			output: {
				filename: '[name].js', // 可配置成'js/[name].js'
				path: __dirname + '/built'
			},
			plugins: [
				new htmlWebpackPlugin({
					template: 'a.html', // 指定打包后的文件关联的html文件
					filename: 'a-[hash].html', // 指定打包后的html文件名
					inject: 'head', // 指定js文件插入位置，默认为body底部
					chunks: ['app','a'],//通过chunks来指定需要载入的js
					excludeChunks: ['b'], // 与chunks二选一
					
					// 自定义参数，可以在html文件中使用ejs语法来获取配置
					title: '标题', 
					date: new Date(),
				}),
				// 多页面
				new htmlWebpackPlugin({
					template: 'b.html', // 指定打包后的文件关联的html文件
					filename: 'b-[hash].html', // 指定打包后的html文件名
					inject: true, // 指定js文件插入位置，默认为body底部
					chunks: ['app','b']
					
					// 自定义参数，可以在html文件中使用ejs语法来获取配置
					title: '标题', 
					date: new Date(),
				}),
			]
		}
	这样就能支持多页面项目的配置，覆盖大部分的业务需要了

### 利用loaders处理资源文件
以es6举例
	
	npm install --save-dev babel-loader babel-core // 安装依赖
	
	// 配置webpack.config.js
	module.exports = {
		entry:'...',
		output:{...},
		module:{
			loaders:[
				{
					test: /\.js$/,
					loader: 'babel',
					query: {
						presets: ['latest'] 
					}
				}
			]
		}
		plugins:[...]
	}
不同的loader配置可以看npm中loader插件的实例或者webpack官网的实例

#### loaders之css文件处理
* css-loader处理css中路径引用等问题
* style-loader动态把样式写入css
* sass-loader scss编译器
* less-loader less编译器
* stylus-loader stylus编译器
* postcss-loader css后处理  
栗子
	
		// 安装
		npm install --save-dev css-loader style-loader sass-loader postcss-loader
		// 配置
		module: {
			loaders: [
				{test: /\.css$/,loader: 'style!css?sourceMap!postcss'},
				{test: /\.sass$/,loader: 'style!css!sass!postcss'}
			]
		}
	
#### loaders之js处理
* babel-loader ES6编译器
* jsx-loader 处理react的jsx文件

		// 安装 
		npm install --save-dev babel-core babel-preset-es2015 babel-loader jsx-loader
		
		// 配置.babelrc文件
		{
			'presets': ['es2015','react'],
			'plugins': ['antd']
		}
		
		// 配置webpack.config.js文件
		loaders: [
			{test: /\.js$/,loaders:'babel',exclude: /node_modules/},
			{test: /\.jsx$/,loaders:'jsx-loader'}
		]
		
#### loaders之图片处理
* url-loader 
	
		// 安装
		npm install --save-dev url-loader
		
		//配置
		module: {
			loaders: [
				loader:[
					{test: /\.(jpg|png)$/,loader:"url?limit=8192"}
				]
			]
		}
		// 图片小于8k的时候转化为base64格式，减少http请求

#### loaders之文件处理
* file-loader

		// 安装
		npm install --save-dev file-loader
		
		// 配置
		module: {
			loaders: [
				{
					test: /\.(png|jpg|jpeg|gif|svg|woff|woff2|ttf|eot)$/,
					loader: 'file'
				}
			]
		}
		
#### loaders之json处理
* json-loader
	
		// 安装
		npm install --save-dev json-loader
		
		// 配置
		module: {
			loaders: [
				{test: /\.json$/,loader: 'json'}
			]
		}

#### loaders之html处理
* raw-loader
	
		// 安装
		npm install --save-dev raw-loader
		
		// 配置
		module: {
			loaders: [
				{test: /\.html$/,loader: 'raw'}
			]
		}