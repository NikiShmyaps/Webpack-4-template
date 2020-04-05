<p align="center">
  <img src="https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667" width="200" title="Webpack">
</p>
<h1 align="center">Webpack Template</h1>
<p>My webpack 4 template. Babel 7v, Sass / postcss (autoprefixer & css-nano & css-mqpacker)</p>
<h2>Project Structure:</h2>

+ `src/index.html` - main file HTML
+ `src/assets/sass` - custom Sass styles
+ `src/assets/img` - images
+ `src/js` - custom app scripts
+ `src/index.js` - the main application file where all the necessary libraries are included / imported
+ `src/static/` - folder with extra static assets that will be copied into output folder

<h2 align="center">Settings</h2>

<h2>Build Setup:</h2>

```
# Install dependencies:
npm install

# Server with hot reload at http://localhost:8081/
npm run dev

# Output will be at dist/ folder
npm run build
```

<h2>Installation:</h2>
<p>To work with Webpack you will need to install dependencies:</p>

+ `npm init` - initialization
+ `npm i webpack webpack-cli webpack-dev-server -D` - main
+ `npm i @babel/core @babel/preset-env babel-loader -D` - Babel7 
+ `npm i css-loader mini-css-extract-plugin` - mini-css-extract-plugin
+ `npm i style-loader sass-loader node-sass` - Webpack 4 SASS
+ `npm i postcss-loader autoprefixer css-mqpacker cssnano` - postcss plugins
+ `npm i webpack-merge -D` - Webpack-merge
+ `npm i file-loader -D` - file-loader
+ `npm i copy-webpack-plugin -D` - opy-webpack-plugin
+ `npm i html-webpack-plugin -D` - html-webpack-plugin

<h2>Customization:</h2>
<p>To configure create six files</p>

+ create a build folder and create files in it
        1 `webpack.base.conf.js`
        2 `webpack.build.conf.js`
        3 `webpack.dev.conf.js`

+ Create the remaining files in the root
    1 `webpack.config.js`
    2 `.babelrc`
    3 `postcss.config.js`

<p>Then enter the following settings in the created files</p>

+ Write to the `webpack.base.conf.js` file:
``` javascript
const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CopyWebpackPlugin = require('copy-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

const PATHS = {
  src: path.join(__dirname, '../src'),
  dist: path.join(__dirname, '../dist'),
  assets: 'assets/'
};

module.exports = {
  externals: {
    paths: PATHS
  },
  entry: {
    main: PATHS.src
  },
  output: {
    filename: `${PATHS.assets}js/[name].js`,
    path: PATHS.dist,
    publicPath: '/'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: '/node_modules/'
      },
      {
        test: /\.(png|jpg|gif|svg)$/,
        loader: 'file-loader',
        options: {
          name: '[name].[ext]'
        }
      },
      {
        test: /\.(woff(2)?|ttf|eot|svg)(\?v=\d+\.\d+\.\d+)?$/,
        loader: 'file-loader',
        options: {
          name: '[name].[ext]'
        }
      },
      {
        test: /\.sass$/,
        use: 
        [
          "style-loader",
          MiniCssExtractPlugin.loader,
          {
            loader: 'css-loader',
            options: { sourceMap:true }
          },
          {
            loader: 'postcss-loader',
            options: { sourceMap:true, config: {path: `./postcss.config.js`} }
          },
          {
            loader: 'sass-loader',
            options: { sourceMap:true }
          }
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: `${PATHS.assets}css/[name].css`
    }),
    new HtmlWebpackPlugin({
      hash: false,
      template: `${PATHS.src}/index.html`,
      filename: './index.html',
      inject: false
    }),
    new CopyWebpackPlugin([
      {from: `${PATHS.src}/${PATHS.assets}img`, to: `${PATHS.assets}img`},
      {from: `${PATHS.src}/${PATHS.assets}fonts`, to: `${PATHS.assets}fonts`},
      {from: `${PATHS.src}/static`, to: ``}
    ])
  ]
};
```

+ Write to the `webpack.config.js` file:
``` js
const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  entry: {
    main: './src/index.js'
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: '/node_modules/'
      },
      {
        test: /\.sass$/,
        use: 
        [
          "style-loader",
          MiniCssExtractPlugin.loader,
          {
            loader: 'css-loader',
            options: { sourceMap:true }
          },
          {
            loader: 'postcss-loader',
            options: { sourceMap:true, config: {path: 'src/js/postcss.config.js'} }
          },
          {
            loader: 'sass-loader',
            options: { sourceMap:true }
          }
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css"
    })
  ],
  devServer: {
    overlay: false
  }
};
```

+ Write to the `webpack.build.conf.js` file:
``` js
const merge = require('webpack-merge');
const baseWebpackConfig = require('./webpack.base.conf');

const buildWebpackConfig = merge(baseWebpackConfig, {
  mode: 'production',
  plugins: []
});

module.exports = new Promise((resolve, reject) => {
  resolve(buildWebpackConfig);
});
```

+ Write to the `webpack.dev.conf.js` file:
``` js
const webpack = require('webpack');
const merge = require('webpack-merge');
const baseWebpackConfig = require('./webpack.base.conf');

const devWebpackConfig = merge(baseWebpackConfig, {
  mode: 'development',
  devtool: 'cheap-module-eval-source-map',
  devServer: {
    overlay: false,
    contentBase: baseWebpackConfig.externals.paths.dist,
    port: 8081,
    overlay: {
      warnings: false,
      errors: true
    }
  },
  plugins: [
    new webpack.SourceMapDevToolPlugin({
      filename: '[file].map'
    })
  ]
});

module.exports = new Promise((resolve, reject) => {
  resolve(devWebpackConfig);
});
```

+ Write to the `postcss.config.js` file:
``` js
module.exports = {
  plugins: [
    require('autoprefixer'),
    require('css-mqpacker'),
    require('cssnano')({
      preset: [
        'default', {
          discardComments: {
            removeAll: true,
          }
        }
      ]
    })
  ]
}
```

+ Write to the `.babelrc` file:
``` js
{
  "presets": [
    "@babel/preset-env"
  ]
}
```
