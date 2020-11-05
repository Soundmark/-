## 1.安装 eslint 和 prettier 插件和工具库

插件是必须的，安装方法很简单，只需要在 vscode 的插件市场搜索安装即可。

工具库安装只需要在项目文件夹中通过终端运行`npm i -D eslint prettier`

## 2.创建配置文件

在项目根目录下创建`.eslintrc.js`(或`.eslintrc.json`，eslint 配置文件支持两种格式)和`.prettierrc`文件

`.eslintrc.js`[配置](https://cn.eslint.org/docs/user-guide/configuring)

```
module.exports = {
  root: true, // 最高级配置
  // 语法支持
  parserOptions: {
    sourceType: 'module', // 模块化
    ecmaVersion: 6, // 支持es6
  },
  extends: ['eslint:recommended'], // 规则扩展,这里用的是eslint推荐配置
  // 全局变量定义
  env: {
    browser: true,
    es6: true,
    jest: true,
    node: true
  },
  // 自定义规则,具体参考https://cn.eslint.org/docs/rules/
  rules: {
    'prefer-const': 'warn',
    'no-undef': 'off',
    'no-var': 'warn',
  },
}
```

`.prettierrc`[配置](https://prettier.io/docs/en/options.html)

```
{
  "tabWidth": 2, // 缩进
  "singleQuote": true, // 使用单引号
  "semi": false, // 不要分号
}
```

创建完这两个文件后，通过右键菜单的`使用...格式化文档`来选择 eslint 或 prettier 来格式化文档。但是这个时候我们只能够分别通过 eslint 或 prettier 的规则来进行格式化，这两者在规则上是会有部分冲突的，所以我们要进一步进行配置。

## 3.使 eslint 和 prettier 两者一起工作

prettier 官方提供了和 linter 协作的解决方案（[Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html)），对于 eslint，有[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)和[eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)以及[prettier-eslint](https://github.com/prettier/prettier-eslint)。

- 方案一（通过 eslint-config-prettier（解决和 eslint 的规则冲突）和 eslint-plugin-prettier（使 prettier 规则可以再 eslint 中使用））
  同时安装这两个库`npm i -D eslint-config-prettier eslint-plugin-prettier`

修改`.eslintrc.js`,在 extends 中添加`plugin:prettier/recommended`（注意需要添加到最后一项，否则可能无法覆盖规则冲突）

```
module.exports = {
  root: true, // 最高级配置
  // 语法支持
  parserOptions: {
    sourceType: 'module', // 模块化
    ecmaVersion: 6, // 支持es6
  },
  extends: ['eslint:recommended', 'plugin:prettier/recommended'], // 规则扩展,这里用的是eslint推荐配置
  // 全局变量定义
  env: {
    browser: true,
    es6: true,
    jest: true,
    node: true
  },
  // 自定义规则,具体参考https://cn.eslint.org/docs/rules/
  rules: {
    'prefer-const': 'warn',
    'no-undef': 'off',
    'no-var': 'warn',
  },
}
```

配置完之后我们只需要将 eslint 设置为默认格式化插件就可以同时使用 eslint 和 prettier 的规则格式化。

方案一十分方便，比较推荐这个方案，唯一感觉不好的是开发的时候你要面临大量的红色波浪线（报错提示），可能开发体验会稍微差点，而且格式化的速度没有单纯用 prettier 快（这个其实影响不大）

- 方案二（prettier-eslint）
  这个方案还是将 eslint 和 prettier 当成两个库使用，通过连续运行它们的格式化命令使它们能够一起工作，它的格式化顺序是先用 prettier 再用 eslint。

安装 prettier-eslint`npm i -D prettier-eslint`

在`package.json`的 script 中配置命令

```
script: {
	"format": "prettier-eslint --write 'src/**/*.{js,jsx}' "
}
```

在终端中运行`npm run format`格式化 src 文件夹下所有的 js 和 jsx 文件。

方案二在使用上可能没有方案一方便，但是它的好处是可以一次性格式化所有目标文件，看个人需求选择吧。

## 结语

以上就是配置 eslint 和 prettier 的所有内容，欢迎交流学习。
