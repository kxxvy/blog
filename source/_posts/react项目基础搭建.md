---
title: react项目基础搭建
date: 2022-06-22
tags: [react]
---

# 项目搭建

首先，使用 create-react-app 创建我们的项目

## 一、安装 React 及 TS

### 1.1. 安装相关库

```shell
yarn create react-app my-app --template typescript
```

<!-- more -->

### 1.2. 安装@types 声明库

```shell
yarn add @types/react @types/react-dom @types/react-redux
```

### 1.3 创建 tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src"]
}
```

### 1.5 安装 cross-env、 craco

1. 安装插件

```shell
yarn add cross-env @craco/craco
```

cross-env 可以让我们跨平台设置和使用环境变量
craco 可以帮助我们修改项目默认配置

2.修改 package.json 文件

- 原本启动时，我们是通过 react-scripts 来管理的
- 现在启动时，我们通过 craco 来管理

```javascript
  "scripts": {
    "start": "cross-env env_config=dev craco start",
    "build": "cross-env env_config=test craco build",
    "build:t": "cross-env env_config=test craco build",
    "build:p": "cross-env env_config=prod craco build",
    "test": "craco test",
    "eject": "react-scripts eject",

  }
```

3.在根目录下创建 craco.config.js 文件用于修改默认配置

```javascript
const path = require("path");
const { whenProd, getPlugin, pluginByName } = require("@craco/craco");

const resolve = (dir) => path.resolve(__dirname, dir);

module.exports = {
  webpack: {
    alias: {
      "@": resolve("src"),
    },
    configure: (webpackConfig, { env, paths }) => {
      let cdn = {
        js: [],
        css: [],
      };
      // 只有生产环境才配置
      whenProd(() => {
        webpackConfig.externals = {
          react: "React",
          "react-dom": "ReactDOM",
        };
        cdn = {
          js: [
            "https://cdnjs.cloudflare.com/ajax/libs/react/18.1.0/umd/react.production.min.js",
            "https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.1.0/umd/react-dom.production.min.js",
          ],
          css: [],
        };
      });

      const { isFound, match } = getPlugin(
        webpackConfig,
        pluginByName("HtmlWebpackPlugin")
      );
      if (isFound) {
        match.userOptions.cdn = cdn;
      }

      // paths.appPath='public'
      paths.appBuild = "dist";
      webpackConfig.output = {
        ...webpackConfig.output,
        path: resolve("dist"),
        publicPath:
          process.env.env_config === "prod"
            ? "https://ydcommon.51yund.com/"
            : process.env.env_config === "test"
            ? "/vapps/test/"
            : "/",
      };

      return webpackConfig;
    },
  },
};
```

## 二. 代码规范

### 2.1. 集成 editorconfig 配置

EditorConfig 有助于为不同 IDE 编辑器上处理同一项目的多个开发人员维护一致的编码风格。

```yaml
# http://editorconfig.org

root = true

[*] # 表示所有文件适用
charset = utf-8 # 设置文件字符集为 utf-8
indent_style = space # 缩进风格（tab | space）
indent_size = 2 # 缩进大小
end_of_line = lf # 控制换行类型(lf | cr | crlf)
trim_trailing_whitespace = true # 去除行首的任意空白字符
insert_final_newline = true # 始终在文件末尾插入一个新行

[*.md] # 表示仅 md 文件适用以下规则
max_line_length = off
trim_trailing_whitespace = false
```

VSCode 需要安装一个插件：EditorConfig for VS Code

![image-20210722215138665](https://tva1.sinaimg.cn/large/008i3skNgy1gsq2gh989yj30pj05ggmb.jpg)

### 2.2. 使用 prettier 工具

Prettier 是一款强大的代码格式化工具，支持 JavaScript、TypeScript、CSS、SCSS、Less、JSX、Angular、Vue、GraphQL、JSON、Markdown 等语言，基本上前端能用到的文件格式它都可以搞定，是当下最流行的代码格式化工具。

1.安装 prettier

```shell
yarn add prettier -D
```

2.配置.prettierrc 文件：

- useTabs：使用 tab 缩进还是空格缩进，选择 false；
- tabWidth：tab 是空格的情况下，是几个空格，选择 2 个；
- printWidth：当行字符的长度，推荐 80，也有人喜欢 100 或者 120；
- singleQuote：使用单引号还是双引号，选择 true，使用单引号；
- trailingComma：在多行输入的尾逗号是否添加，设置为 `none`；
- semi：语句末尾是否要加分号，默认值 true，选择 false 表示不加；

```json
{
  "printWidth": 80, //单行长度
  "tabWidth": 2, //缩进长度
  "useTabs": false, //使用空格代替tab缩进
  "semi": false, //句末使用分号
  "singleQuote": true, //使用单引号
  "quoteProps": "as-needed", //仅在必需时为对象的key添加引号
  "jsxSingleQuote": true, // jsx中使用单引号
  "trailingComma": "all", //多行时尽可能打印尾随逗号
  "bracketSpacing": true, //在对象前后添加空格-eg: { foo: bar }
  "jsxBracketSameLine": true, //多属性html标签的‘>’折行放置
  "arrowParens": "always", //单参数箭头函数参数周围使用圆括号-eg: (x) => x
  "requirePragma": false, //无需顶部注释即可格式化
  "insertPragma": false, //在已被preitter格式化的文件顶部加上标注
  "proseWrap": "preserve",
  "htmlWhitespaceSensitivity": "ignore", //对HTML全局空白不敏感
  "vueIndentScriptAndStyle": false, //不对vue中的script及style标签缩进
  "endOfLine": "lf", //结束行形式
  "embeddedLanguageFormatting": "auto" //对引用代码进行格式化
}
```

3.创建.prettierignore 忽略文件

```
# Ignore artifacts:

/dist/\*
.local
.output.js
/node_modules/\*\*

**/\*.svg
**/\*.sh

/public/\*
```

4.VSCode 需要安装 prettier 的插件

![image-20210722214543454](https://tva1.sinaimg.cn/large/008i3skNgy1gsq2acx21rj30ow057mxp.jpg)

5.测试 prettier 是否生效

- 测试一：在代码中保存代码；
- 测试二：配置一次性修改的命令；

在 package.json 中配置一个 scripts：

```json
    "prettier": "prettier --write ."
```

### 2.3. 使用 ESLint 检测

1. 先安装 eslint

```shell
yarn add eslint -g
```

2. 这个时候我们需要声明一个 eslintrc.js 文件：

```javascript
module.exports = {
  parser: "@typescript-eslint/parser", // 定义ESLint的解析器
  extends: ["plugin:prettier/recommended"], //定义文件继承的子规范
  plugins: ["@typescript-eslint", "react-hooks", "eslint-plugin-react"], //定义了该eslint文件所依赖的插件
  env: {
    //指定代码的运行环境
    browser: true,
    node: true,
  },
  settings: {
    //自动发现React的版本，从而进行规范react代码
    react: {
      pragma: "React",
      version: "detect",
    },
  },
  parserOptions: {
    //指定ESLint可以解析JSX语法
    ecmaVersion: 2019,
    sourceType: "module",
    ecmaFeatures: {
      jsx: true,
    },
  },
  rules: {
    // 自定义的一些规则
    "prettier/prettier": "error",
    "linebreak-style": ["error", "unix"],
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn",
    "react/jsx-uses-react": "error",
    "react/jsx-uses-vars": "error",
    "react/react-in-jsx-scope": "error",
    "valid-typeof": [
      "warn",
      {
        requireStringLiterals: false,
      },
    ],
  },
};
```

parser 配置：插件@typescript-eslint/parser 让 ESLint 对 TypeScript 的进行解析。

```shell
yarn add @typescript-eslint/parser -D
```

extends 配置：为了防止 eslint 和 prettier 的规则发生冲突，我们需要集成两者则设置为['plugin:prettier/recommended']。

```shell
yarn eslint-config-prettier eslint-plugin-prettier -D
```

到此为止 ESlint+Prettier 的配置就结束了，接下来我们需要提交我们的代码了。

### 2.4. 规范 git 提交

在将代码提交到仓库之前我们希望我们的代码全部进行代码格式化和代码质量检查，为此我们需要使用到一个工具对我们 git 缓存区最新改动过的文件进行格式化和 lint 规则校验。

```shell
yarn add husky@4.3.0 lint-staged -D
```

tips: 这里要注意一下的是 husky 最好指定一下版本

随后我们需要修改 package.json 文件:

```json
"husky": {
  "hooks": {
    "pre-commit": "lint-staged",
  }
},
"lint-staged": {
  "*.{ts,tsx,js,jsx}": [
    "prettier --write .",
    "eslint --config .eslintrc.js"
  ]
}
```

这样我们使用 ESlint 针对 ts,tsx,js,jsx 结尾的文件进行代码质量检查，并且使用 prettier 对所有的文件进行代码格式化。接下来我们就该为此次提交写 commit 了。

如果你发现线上出现 bug 需要回滚的时候，结果 commit 记录全是 fix 或者是修改代码这种提交记录的时候估计心态是炸的，这时候我们需要规范我们的 Git commit 信息。

首先我们先安装所需要的库：

```shell
yarn add @commitlint/config-conventional @commitlint/cli -D
```

之后我们创建 commitlint.config.js 文件：

```javascript
const types = [
  "build", // 修改项目的的构建系统（xcodebuild、webpack、glup等）的提交
  "ci", // 修改项目的持续集成流程（Kenkins、Travis等）的提交
  "chore", // 构建过程或辅助工具的变化，翻译为日常琐事
  "docs", // 文档提交（documents）
  "feat", // 新功能（feature）
  "fix", // bug已经修复。 适合于一次提交直接修复问题
  "to", // bug还未修复。适合于多次提交。最终修复问题提交时使用fix
  "pref", // 优化相关，比如提升性能、体验（performance）
  "refactor", // 重构（即不是新增功能，也不是修改bug的代码变动）
  "revert", // 回滚到上一个版本
  "style", // 不影响程序逻辑的代码修改、主要是样式方面的优化、修改
  "test", // 测试相关的开发
  "sync", // 同步主线或分支的Bug
];

const typeEnum = {
  rules: {
    "type-enum": [2, "always", types],
  },
  value: () => types,
};

module.exports = {
  extends: ["@commitlint/config-conventional"],
  rules: {
    "type-case": [0],
    "type-empty": [2, "never"],
    "scope-empty": [0],
    "scope-case": [0],
    "subject-full-stop": [0, "never"],
    "subject-case": [0, "never"],
    "header-max-length": [0, "always", 72],
    "type-enum": typeEnum.rules["type-enum"],
  },
};
```

最后我们在刚刚配置 pre-commit 之后，增加下面的信息：

```json
"husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      // commit信息
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  },
```

HUSKY_GIT_PARAMS 为我们 commit 的信息 ，然后 commitlint 去会对这些信息进行 lint 校验。

以上。
