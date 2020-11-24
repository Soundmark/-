# 前言

前段时间在项目中配置 jest，一直遇到`SyntaxError: Cannot use import statement outside a module`的问题，查了很多资料，虽然最后发现导致错误的原因比较简单，但是既然都花了时间去研究，就记录一下各种解决方法吧。

# 解决方法

## 1.没有用 babel 转换 es6 语法

jest 是运行在 node 环境的，所以不支持 es6 语法，我们需要通过配置 babel 来讲 es6 语法转换为 es5 语法。<br>
安装 babel-jest、@babel/core、@babel/preset-env<br>
`npm i babel-jest @babel/core @babel/preset-env -D`

在根目录新建 babel.config.js 文件，添加如下配置：

```
module.exports = {
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": true
        }
      }
    ]
  ]
}
```

> 注意：这是 babel7 的配置，babel6 有所不同，如果是用 babel6 可以到 jest 文档看看 babel6 配置。
> 假如你项目中用到 typescript，还需要安装 ts-babel<br>
> `npm i ts-babel -D`

这个时候需要在 jest.config.js 中添加如下配置：

```
module.exports = {
  ...其他配置...
  transform: {
    '^.+\\.jsx?$": "babel-jest', //这个是jest的默认配置
    '^.+\\.ts?$': 'ts-jest', //typescript转换
  }
}
```

## 2.依赖定位错误

这个是我所犯的错误，姑且将原因归结为依赖定位错误吧，原因是跟 alias 有关。相信用 cli 工具搭建过项目的同学都知道用`@`来代替根目录的 src 目录这个用法吧，既然你在 webpack 中配置了 alias，那在 jest 中也要进行相应配置，在 jest.config.js 中添加如下配置：

```
module.exports = {
  ...其他配置...
  moduleNameMapper:{
    '@': '<rootDir>/src'
  }
}
```

大家也许会觉得这样子就 ok 了，但是有没有发现 babel 的库是`@`开头的，问题来了，因为 jest 中配置了`@`别名，因此也会将@babel 开头的库也一起转换了，导致无法使用 babel，因此就相当于是没有安装 babel，所以我们需要进行如下改造：

```
module.exports = {
  ...其他配置...
  moduleNameMapper:{
    '@/(.*)$': '<rootDir>/src/$1'
  }
}
```

## 3.玄学原因

我在 jest 的 github 中看到好几个 issue，发起的人给出的配置正常，但是仍然出现这个报错，然后有人说自己也是一样的问题，然后通过将 babel 的配置文件`.babelrc`改为`babel.config.js`就可以了，我觉得这个比较玄学，因为 babel7 仍然兼容`.babelrc`文件，不知道是不是真的，姑且康康。

## 4.需要转换 node_modules 中的文件

jest 默认配置是不转换 node_modules 文件夹的，因为一般来说我们通过 npm 下载的库都是已经转换过的了，但是也有例外，像 react-native 相关的一些库就会出现这种情况，遇到这种情况，拿 react-native 举例，我们需要在 jest.config.js 中进行如下配置：

```
module.exports = {
  ...其他配置...
  'transformIgnorePatterns': [
    "node_modules/(?!(react-native|my-project|react-native-button)/)"
  ]
}
```

# 结语

希望对大家有帮助，求点赞！
