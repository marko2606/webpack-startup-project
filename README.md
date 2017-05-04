
# Webpack Demo App

Dummy App that shows how to setup JS project and configure Webpack

## GIT branches
```
git branch

# checkout master to get latest version of complete demo
git checkout master

# checkout workshop1 to get version from first workshop
git checkout workshop1
```

## Table of Contents
- [Requirements](#requirements)
- [Basic information](#basic-information)
- [Creating a project](#creating-a-project)
- [Links](#links)


## Requirements

You need to have NodeJS installed.

## Basic information

Webpack is a tool to build JavaScript modules in your application. It simplifies your workflow by quickly constructing a dependency graph of your application and bundling them in the right order. Webpack can be configured to customize optimizations to your code, to split vendor/css/js code for production, run a development server that hot-reloads your code without page refresh and many such cool features.

![alt text](https://webpack.github.io/assets/what-is-webpack.png)

### Configuration

All of the configuration information are stored in webpack.config.js file. Config file needs to export a JS object that holds the configuration of webpack. Use NodeJS sytax for exporting.

example of webpack.config.js file
```
module.exports = {
	entry: {
		// entry configuration
	},
	output: {
		// output configuration
	},
	module: {
        // module/loaders configuration
	},
	plugins: [
		// plugin configuration
	]
};

```

### 4 basic concepts for configuring webpack. 

1. Entry point 
2. Output point 
3. Module - Loaders 
4. Plugins 


These concepts are properties of a weback module.

1. Entry point
    - Instructs the webpack where to start bundling the files. There can be more that one entry point, and it can be in the format of a string, an object or an array.

    ```
    module.exports = {

        ...

        entry: './src/index.js'

        ...

    }
    
    ```
2. Output point
    - JS object where we configure what the output should look like, where to store it and what to name it. 
    - Use path property to tell webpack where to store the files. Define path using nodejs syntax at the beginning of the webpack.config.js file. Call path.resolve(__dirname, 'dist'). '__dirname' refers to current directory, second argument refers to subdirectory where we want to store out files. Why are we using the output path.resolve method? The output path needs to be an absolute path. Webpack needs to write something there, to create something.
    - To set the name of the file we want to create we use filename property.
    ```

    var path = require('path');

    module.exports = {

        ...
        
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: 'bundle.js',
        }

        ...

    }

    ```
3. Modules - Loaders
    - Module needs some rules property to tell it how to behave with certain file types.
    - test property tells webpack what rules apply on what file types.
    - Loader property tells the webpack what loader should use for that file(s). In this case we are using babel loader so we can use es6 syntax.
    - When using multiple loaders order of that file in array is important because webpack will run your loaders in reverse order!


```
    module.exports = {

        ...

          module: {
            rules: [
                {
		    test: /\.js$/,
		    exclude: /node_modules/,
		    use: {
			use: ['babel-loader'],
			options: {
			presets: ['env']
			}
		    }
		}
            ]
	  }

        ...

    }

```

4. Plugins
    - Similar to loaders. Plugin are applied to your bundle before it is outputted.
    - Usage: To minify the code, dynamically put js/css/images to you html... 

```
    const HtmlWebpackPlugin = require('html-webpack-plugin');

    module.exports = {

        ...

          plugins: [
            new HtmlWebpackPlugin({
		template: config.HTML_ENTRY_POINT
		})
          ]

        ...

    }


```

## Creating a project

Initialize a package.json. -y answers all the questions with 'yes'

```
npm init -y
```

### Install webpack [link](https://webpack.js.org/guides/installation/)

```
npm install --save-dev webpack
```

create a build script in package.json

```
"script": {
    "build": "webpack"
}
```

### Adding html-webpack-plugin to attach bundle files to index.html [link](https://webpack.js.org/plugins/html-webpack-plugin/)

```
npm install --save-dev html-webpack-plugin
```

Create webpack.config.js file in root directory.

```
    var path = require('path');
    const config = {
	HTML_ENTRY_POINT: './src/index.html',
	JS_ENTRY_POINT: './src/index.js',
	OUTPUT_PATH: 'dist',
	OUTPUT_JS_FILENAME: 'bundle.js',
	OUTPUT_CSS_FILENAME: 'bundle.css'
};

    module.exports = {
        entry: './src/index.js',
        output: {
            path: path.resolve(__dirname, config.OUTPUT_PATH),
            filename: config.OUTPUT_JS_FILENAME
        }

    }
```

Add HtmlWebpackPlugin const to config file.

```
const HtmlWebpackPlugin = require('html-webpack-plugin');
```

Activate plugin

```
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    module.exports = {

        ...

     plugins: [
          new HtmlWebpackPlugin({
              template: config.HTML_ENTRY_POINT
          })
      ]

        ...

    }

```

Webpack will bundle our files into dist/bundle.js and also create a copy of index.html based on our configuration.
run build

open index.html in dist

### Install rimraf [link](https://www.npmjs.com/package/clean-webpack-plugin)

rimraf will delete all previous bundle files every time when we run webpack. 

```
npm install --save-dev rimraf
```
Add rimraf to build script
```
scripts": {
    "clean": "rimraf dist",
    "build": "npm run clean && webpack",
}
```

### Add css [link](https://webpack.js.org/loaders/css-loader/)

```
npm install --save-dev style-loader css-loader 
```
css-loader takes care of importing css files

css-style implements css rules on html file.



### Adding sass [link](https://webpack.js.org/loaders/sass-loader/)

```
npm install --save-dev sass-loader node-sass 
```

### Adding extract-text-webpack-plugin to extract CSS to bundle.css [link](https://www.npmjs.com/package/extract-text-webpack-plugin)

```
npm install --save-dev extract-text-webpack-plugin
```

We need to add a rule to webpack module in order to handle css files and include it into html.

```
const ExtractTextPlugin = require('extract-text-webpack-plugin');
...

module: {
    rules: [
    	{
	test: /\.scss$/,
	use: ExtractTextPlugin.extract({
		use: ['css-loader','sass-loader']
	     })
	}
    ]
},
plugins: [
		new HtmlWebpackPlugin({
			template: config.HTML_ENTRY_POINT
		}),
		new ExtractTextPlugin({
		    filename: config.OUTPUT_CSS_FILENAME
		})
	]

...

```

### Adding bootstrap [link](https://webpack.js.org/loaders/css-loader/#alias)

```
npm install --save-dev bootstrap-sass
npm install --save-dev file-loader
```

Add options to loader to handle bootstrap files.

```
module: {
	rules: [
		{
			test: /\.js$/,
			exclude: /node_modules/,
			use: {
				loader: 'babel-loader',
				options: {
					presets: ['env']
				}
			}
		},
		{
			test: /\.scss$/,
			use: ExtractTextPlugin.extract({
				use: [
					{
						loader: 'css-loader',
						options: {
							alias: {
								'../fonts/bootstrap': 'bootstrap-sass/assets/fonts/bootstrap'
							}
						}
					}, 
					{
						loader: 'sass-loader',
						options: {
							includePaths: [
								path.resolve('./node_modules/bootstrap-sass/assets/stylesheets')
							]
						}
					}
				]
			})
		  }	
		]	
	}

```

### Adding babel [link](https://webpack.js.org/loaders/babel-loader/)
```
npm install --save-dev babel-loader babel-core babel-preset-env 
```
Add a rule to module to handle js files

```
module: {
	rules: [
		{
			test: /\.js$/,
			exclude: /node_modules/,
			use: {
				loader: 'babel-loader',
				options: {
					presets: ['env']
			}
		}
	      ]
	}
```

### Adding Webpack Dev Server [link](https://webpack.js.org/configuration/dev-server/)
```
npm install --save-dev webpack-dev-server
```

### Build for production and adding to npm scripts [link](https://webpack.js.org/guides/production-build/)

webpack -p command will provide us with a minified production file.

webpack-dev-server command will run server localy and will not create a bundle files in dist folder. Instead those files are stored in memory.
```
 "scripts": {
    "clean": "rimraf dist",
    "build": "npm run clean && webpack",
    "build-prod": "npm run clean && webpack -p",
    "serve": "webpack-dev-server"
  },
```
### Webpack devtool - enhance the debugging process [link](https://webpack.js.org/configuration/devtool/#devtool)

```
module.exports = {
...

  devtool: "#inline-source-map",
  
...
}
```


## Links
- [Get Started](https://webpack.js.org/guides/get-started/)
- [NPM](https://www.npmjs.com)
- [Webpack](https://webpack.js.org)
- [Babel](https://babeljs.io)
- [Webpack 2: The Complete Developer's Guide](https://www.udemy.com/webpack-2-the-complete-developers-guide/learn/v4/overview)
- [Youtube tutorial 1](https://www.youtube.com/watch?v=GU-2T7k9NfI&list=PL55RiY5tL51rcCnrOrZixuOsZhAHHy6os)
- [Youtube tutorial 2](https://www.youtube.com/watch?v=JdGnYNtuEtE&list=PLkEZWD8wbltnRp6nRR8kv97RbpcUdNawY)

