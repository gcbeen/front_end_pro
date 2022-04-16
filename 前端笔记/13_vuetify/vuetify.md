# vuetify【放弃，坑多】

## sass-loader 安装



```sh
# "sass": "^1.49.9",
# "sass-loader": "^7.3.1",
# "node-sass": "^4.13.1",

npm install sass sass-loader@7.3.1 node-sass@4.13.1 fibers deepmerge -D
```



一旦安装，请找到你的 `webpack.config.js` 文件，并将下面的片段复制到 **rules** 。如果已经存在 **sass** 规则，你可能需要应用下面的一些或所有的更改。



```js
// webpack.config.js

module.exports = {
  rules: [
    {
      test: /\.s(c|a)ss$/,
      use: [
        'vue-style-loader',
        'css-loader',
        {
          loader: 'sass-loader',
          // Requires sass-loader@^7.0.0
          options: {
            implementation: require('sass'),
            fiber: require('fibers'),
            indentedSyntax: true // optional
          },
          // Requires sass-loader@^8.0.0
          options: {
            implementation: require('sass'),
            sassOptions: {
              fiber: require('fibers'),
              indentedSyntax: true // optional
            },
          },
        },
      ],
    },
  ],
}
```

