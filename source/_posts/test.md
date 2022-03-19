---
title: webpack配置示例
date: 2022-03-19 15:59:40
tags: test
---

## webpack 配置参考

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const webpack = require("webpack"); // 用于访问内置插件
// 由于output的path只支持绝对路径，所以需要引入path模块进行合并路径
const CopyWebpackPlugin = require("copy-webpack-plugin");
module.exports = {
  // 设置模式
  //development | production
  mode: "development",
  // devtool:"eval",
  // 设置source方便调试
  devtool: "source-map",
  entry: "./src/main.js",
  output: {
    path: path.resolve(__dirname, "./dist"),
    //设置js打包路径及名称
    filename: "js/bundle_[hash:6].js",
    //也可以在这里设置打包路径
    // assetModuleFilename: "img/[name]-[hash:6][ext]",
  },
  module: {
    rules: [
      // {
      //   test: /\.css$/, // 使用正则表达式对资源做匹配
      //   //1.loader的写法（语法糖）
      //   // loader: "css-loader",
      //   use: [
      //     //loader 从右到左（或从下到上）地取值(evaluate)/执行(execute)。
      //     {
      //       loader: "style-loader",
      //     },
      //     {
      //       loader: "css-loader",
      //       options: {},
      //     },
      //     {
      //       loader: "postcss-loader",
      //       options: {
      //         postcssOptions: {
      //           plugins: [require("autoprefixer")],
      //         },
      //       },
      //     },
      //   ],
      // },
      {
        test: /\.(less|css)$/,
        use: [
          "style-loader",
          {
            loader: "css-loader",
            options: {
              esModule: false, //解决生成多余文件问题
              importLoaders: 1,
            },
          },
          "postcss-loader",
          "less-loader",
        ],
      },
      // {
      //   test: /\.(png|jpeg|jpg|gif|svg)$/,
      //   use: [
      //     {
      //       loader: "url-loader",
      //       options: {
      //         // outputPath: "img",
      //         name: "img/[name]_[hash:6].[ext]",
      //         limit: 100 * 1024,
      //       },
      //     },
      //   ],
      // },
      {
        test: /\.(png|jpeg|jpg|gif|svg)$/,
        type: "asset",
        generator: {
          filename: "img/[name]_[hash:6][ext]",
        },
        parser: {
          // 设置转化为base64编码的大小限制
          dataUrlCondition: {
            maxSize: 100 * 1024,
          },
        },
      },
      //fileloader方式打包字体资源
      // {
      //   test: /\.(eot|ttf|woff2?)$/,
      //   use: [
      //     {
      //       loader: "file-loader",
      //       options: {
      //         // outputPath: "font",
      //         name: "font/[name]_[hash:6].[ext]",
      //       },
      //     },
      //   ],
      // },
      // asset module type方式打包字体资源
      {
        test: /\.(eot|ttf|woff2?)$/,
        type: "asset/resource",
        generator: {
          filename: "font/[name]_[hash:6][ext]",
        },
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "./public/index.html"),
      title: "test",
      options: {},
    }),
    new CleanWebpackPlugin(),
    new webpack.DefinePlugin({
      BASE_URL: "'./'",
    }),
    new CopyWebpackPlugin({
      patterns: [
        {
          from: "public",
          // to: "./",
          globOptions: {
            ignore: ["**/index.html"],
          },
        },
      ],
    }),
  ],
};
```
