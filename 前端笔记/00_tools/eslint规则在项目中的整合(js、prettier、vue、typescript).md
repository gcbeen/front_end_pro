# eslint规则在项目中的整合(js、prettier、vue、typescript)

- VS Code安装插件：Eslint、Vetur、prettier
- 在项目根目录创建编辑器的项目级配置文件`.vscode/settings.json`
- 创建eslint配置文件`.eslintrc.js`
- 创建prettier配置文件`.prettierrc.js`



## 通过eslint预设规则校验js代码

### 1. 配置对js代码的lint

安装依赖

```sh
# eslint-plugin-standard has been deprecated
#npm install eslint eslint-plugin-standard  -D

# eslint@^7.12.1
# eslint-config-standard@16.0.3
# eslint-plugin-import@^2.22.1
# eslint-plugin-node@^11.1.0
# eslint-plugin-promise@^4.2.1 || ^5.0.0
npm install eslint eslint-config-standard  -D
```



使用`.eslintrc.js`文件配置规则

```js
// .eslintrc.js

module.exports = {
  // 当前目录为根目录，ESLint规则被限定在根目录下
  root: true,
  // env 启用ESLint 检测的环境
  env: {
    browser: true,
    commonjs: true,
    es2021: true,
    node: true,
  },
  "extends": [
    "standard"
  ]
};
```



> 以上是使用网上流行的lint规则包`eslint-plugin-standard`，当然也可以使用其他预设lint，只需安装对应的依赖即可；eslint官方也有预定义的lint规则，比如`eslint:recommended`，把以上的`standard`改成`eslint:recommended`即可，这个不需要安装其他依赖。



### 2. 配置对vue代码的lint

需要使用Vue官方提供的[eslint-plugin-vue](https://eslint.vuejs.org/)包来对vue文件进行lint操作，因为eslint默认只能识别js文件

安装依赖

```sh
npm install eslint-plugin-vue -D
```



使用`.eslintrc.js`文件配置规则

```js
module.exports = {
  "parser": "vue-eslint-parser", //解析.vue文件
  "extends": [
    "plugin:vue/recommended"
  ]
};
```



若希望对`.vue`文件中的`<script>`代码进行代码校验，则需要在`parserOptions.parser`去定义相应的解析器，具体可查看[Vue 官方文档关于parserOptions的配置](https://github.com/vuejs/vue-eslint-parser#parseroptionsparser)

安装依赖

```sh
npm install babel-eslint -D
```



若是希望使用babel去校验`<script>`内代码，则需要在以上配置基础上添加该配置：

```js
module.exports = {
    "parser": "vue-eslint-parser",
    "parserOptions": {
        "parser": "babel-eslint",
        "sourceType": "module",
        "allowImportExportEverywhere": false
    }
}
```



### 编辑器处理

在项目根目录中创建项目级的编辑器配置文件`.vscode/settings.json`，然后做以下两个处理：

- 配置`eslint.validate`扩展名的选项以检查`.vue`文件，因为扩展名仅针对`*.js`或`*.jsx`默认为文件
- 设置`"vetur.validation.template": false`为避免默认的Vetur模板验证

示例**.vscode / settings.json**

```json
{
    "eslint.alwaysShowStatus": true, // 在下框显示ESLint图标
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true // 开启自动保存修复
    },
    "eslint.run": "onSave", // 也可以 "onType“，边写边检查
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        // "html",
        "vue"
    ],
    "vetur.validation.template": false,
    "editor.detectIndentation": false,
    "editor.tabSize": 2,
    "prettier.useEditorConfig": false,
}
```



## 通过prettier去统一代码风格

prettier的lint整合步骤大致如下，具体可以了解prettier的[官方文档](https://github.com/prettier/eslint-plugin-prettier)：

### 1. 安装依赖

```sh
npm install --save-dev eslint-plugin-prettier eslint-config-prettier
npm install --save-dev --save-exact prettier
```



> `eslint-plugin-prettier`用于对文件代码进行风格检查
>
> `eslint-config-prettier`用于关闭其他插件与prettier出现冲突的lint，然后以prettier为准

> –save-exact 意思是锁定prettier在package.json的版本，不会出现版本兼容符号`^/~`



### 2. 使用`.eslintrc.js`文件配置规则

```js
module.exports = {
  extends: [
      // ...其他lint
      // 为了保证格式化后代码都以prettier为准，把这两项配置放到数组最后
      "plugin:prettier/recommended",
      "prettier"
  ]
}
```



等同于

```js
module.exports = {
  "extends": ["prettier"],
  "plugins": ["prettier"],
  "rules": {
     "prettier/prettier": "error",
     "arrow-body-style": "off",
     "prefer-arrow-callback": "off"
    }
}
```



### 3. 创建prettier配置文件`.prettierrc.js`

```js
// prettier格式化配置文件
module.exports = {
  "singleQuote": true,// 使用单引号
  "tabWidth": 2,// tab缩进大小,默认为2
  "useTabs": false,//使用tab缩进，默认false
  "semi": false,// 结尾不用分号
  "printWidth": 120,// 超过最大值换行
  "endOfLine": "auto"// 结尾是 \n \r \n\r auto
}
```



> 虽然直接在eslint配置文件中去配置prettier的规则，但是不建议这样配置，而应该把配置写到单独一个文件中。
>
> [prettier的官方文档](https://github.com/prettier/eslint-plugin-prettier#options)有说到：由于部分编辑器插件（如prettier-vscode）只会读取文件.prettier，而不会去读取eslint中的配置。



prettier设置字段项如下说明

```js
module.exports = {
    "printWidth": 100, // 超过最大值换行
    "tabWidth": 4, // 缩进字节数
    "useTabs": false, // 缩进不使用tab，使用空格
    "semi": true, // 句尾添加分号
    "singleQuote": true, // 使用单引号代替双引号
    "proseWrap": "preserve", // 默认值。因为使用了一些折行敏感型的渲染器（如GitHub comment）而按照markdown文本样式进行折行
    "arrowParens": "avoid", // (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号
    "bracketSpacing": true, // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
    "disableLanguages": ["vue"], // 不格式化vue文件，vue文件的格式化单独设置
    "endOfLine": "auto", // 结尾是 \n \r \n\r auto
    "eslintIntegration": false, // 不让prettier使用eslint的代码格式进行校验
    "htmlWhitespaceSensitivity": "ignore",
    "ignorePath": ".prettierignore", // 不使用prettier格式化的文件填写在项目的.prettierignore文件中
    "jsxBracketSameLine": false, // 在jsx中把'>' 是否单独放一行
    "jsxSingleQuote": false, // 在jsx中使用单引号代替双引号
    "parser": "babylon", // 格式化的解析器，默认是babylon
    "requireConfig": false, // Require a 'prettierconfig' to format prettier
    "stylelintIntegration": false, // 不让prettier使用stylelint的代码格式进行校验
    "trailingComma": "es5", // 在对象或数组最后一个元素后面是否加逗号（在ES5中加尾逗号）
    "tslintIntegration": false // 不让prettier使用tslint的代码格式进行校验
}
```



### Prettier相关npm包和VSCode插件

ESLlint是我们常用的代码质量检查工具，当然它也有代码风格检查的能力。ESLint在代码风格这块做到并不够好，所以Prettier就出现了。官方自称Prettier是一个“有主见”的代码格式化工具。

#### eslint-config-prettier

首先我们需要使用 eslint-config-prettier 来关掉 (disable) 所有和 Prettier 冲突的 ESLint 的配置（这部分配置就是上面说的，格式问题的配置，所以关掉不会有问题），方法就是在 .eslintrc 里面将 prettier 设为最后一个 extends

```js
// .eslintrc.js    
module.exports = {      
    "extends": ["prettier"] // prettier 一定要是最后一个，才能确保覆盖    
}

```

#### eslint-plugin-prettier

(可选，推荐) 然后再启用 eslint-plugin-prettier ，将 prettier 的 rules 以插件的形式加入到 ESLint 里面并继承eslint-config-prettier的规则。这里插一句，为什么"可选" ？当你使用 Prettier + ESLint 的时候，其实格式问题两个都有参与，disable ESLint 之后，其实格式的问题已经全部由 prettier 接手了。那我们为什么还要这个 plugin？其实是因为我们期望报错的来源依旧是 ESLint ，使用这个，相当于把 Prettier 推荐的格式问题的配置以 ESLint rules 的方式写入，这样相当于可以统一代码问题的来源。

```js
// .eslintrc.js
module.exports = {      
    "plugins": ["prettier"],      
    "rules": {        
        "prettier/prettier": "error"      
    }    
}

```



将上面两个步骤和在一起就是下面的配置，也是官方的推荐配置

```js
// .eslintrc.js
module.exports = {
  "extends": ["plugin:prettier/recommended"]
}

```



#### VSCode插件

VSCode可以安装prettier的插件，安装这个插件之后就算我们的项目没有安装prettier的npm包，我们也可以利用prettier来格式化我们项目中的代码：



适用于prettier的npm包的代码规范设置，插件也同样适用，可以直接在VSCode的首选项中的prettier插件部分进行设置。除了在VSCode首选项中设置，它还可以识别prettier的配置文件，比如**.prettierrc.js[推荐]**或者package.json中的prettier字段，并读取它们里面的配置用于格式化代码

1. prettier插件通过prettier的配置文件、package.json中的prettier属性和prettier插件的选项来设置prettier的代码规范
2. prettier的npm包通过prettier的配置文件和package.json中的prettier属性来设置prettier的代码规范

ESLint除了通过**prettier的配置文件**和package.json中的prettier属性设置prettier的代码校验规则外，还可以在.eslintrc.js中设置prettier代码规范，当然这个设置只在对ESLint校验代码时生效，比如：


```js
// .esllintrc.js
module.exports = {
  extends: [
    'plugin:prettier/recommended'
  ],
  rules: {
    'prettier/prettier': ['warn', { semi: false, singleQuote: true }]
  }
}

```



## 通过插件去校验vue和typescript的代码风格

### 1. 安装依赖

```sh
npm i --save-dev eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin
```



### 2. 使用`.eslintrc.js`文件配置规则

```js
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
  	// ...其他规则
    'plugin:@typescript-eslint/recommended',
  ]
}
```





> 注意！注意！
>
> 1. 以上配置的parse字段`@typescript-eslint/parser`表示eslint需要使用该解释器去解析源代码，因为默认的解析器不识别ts文件的代码
> 2. 若项目是Vue项目，也就是eslint集成了`eslint-plugin-vue`去校验vue代码，由于`eslint-plugin-vue`插件也使用parse配置项（`vue-eslint-parser`），因此集成typescript就不能替换这一项，需要把这个值配置到`parserOptions`，即如下：



```js
module.exports = {
  root: true,
  parser: "vue-eslint-parser",
  parserOptions: {
	  parser: "@typescript-eslint/parser",
    sourceType: "module"
  }
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
  	// ...其他规则
    'plugin:@typescript-eslint/recommended',
  ]
}
```





## 文件保存时自动格式化

实现文件保存时自动格式化内容，把代码格式化为符合prettier风格，需要修改编辑器配置文件`.vscode/settings.json`

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  }
}
```





## git提交时eslint



我们使用husky和lint-staged两个库进行完成git钩子commit时的校验与格式化处理，只有eslint通过时，代码才允许commit，这样保证了代码的风格统一。

- [husky](https://www.npmjs.com/package/husky)可用于在执行git命令操作时自定义命令，比如在代码被`commit`到本地仓库前，可以让我们定义一些预检查和格式化等工作，
- [lint-staged](https://www.npmjs.com/package/lint-staged)库是一个可对处于git暂存区文件（被`git add`的文件）进行自定义逻辑的库



> 要求：husky实际上是利用了git提供的Hooks的技术即通过修改.git/hooks文件来实现的，所以要先有`.git`目录，然后再安装husky使用，否则会导致husky不生效



### 1. 安装依赖

```sh
npm i -D husky@4.3.6 lint-staged
```

这里husky的版本写死是由于，4.0版本和5.0版本的配置不一样，如果直接安装会安装到5.0版本，而5.0版本配置稍微复杂点，有兴趣可以去[husky官网](https://typicode.github.io/husky/#/?id=automatic-recommended)研究一下。

### 2. 创建`.huskyrc`文件

以下是husky4.0版本的配置

```json
{
    "hooks": {
      "pre-commit": "lint-staged"
    }
}
```



以上代码表示执行`git commit`命令时，让lint-stated库对处于git暂存区的文件进行逻辑处理



### 3. 创建`.lintstagedrc`文件

```json
{
  "src/**/*.*": [
    "eslint --fix",
    "git add"
  ]
}
```



> 配置文件中的每一项的键表示对暂存区的文件根据匹配规则过滤，值为对过滤出来的文件进行命令处理。
>
> 以上的定义为：对处于暂存区的文件列表，执行匹配过滤操作，文件路径必须符合为在src目录下的任意文件，然后对这些过滤后的文件执行数组中的命令eslint修复命令，然后再把修复好的代码`git add`。
>
> 当不报错时才算执行`git commit`代码成功，否则中断提交代码的操作并输出报错



### 4. 添加format命令（非必须）

往package.json文件中添加format命令

```json
{
	scritps: {
		"format": "eslint --fix --ext xxx src"
	}
}
```



> 注意，以上xxx位置填写所有需要lint的文件扩展名，比如我需要lint并且自动修复错误的文件有ts文件，vue文件，则命令脚本为`eslint --ext .ts,.vue src`
