# Vue3 + TS 项目搭建规范
## 一、代码规范
### 1.1 集成editorconfig配置
EditorConfig 有助于为不同的IDE编辑器上处理同一项目的多个开发人员维护一致的代码风格

```.editorconfig
# https://editorconfig.org

root = true

[*] # 表示所有的文件都适用
charset = utf-8 # 设置文件字符集为 utf-8
indent_style = space # 缩进风格(tab | space)
indent_size = 2 # 缩进大小
end_of_line = lf # 控制换行类型（lf | cr | crlf)
insert_final_newline = true # 去除首行的任意空白字符
trim_trailing_whitespace = true # 始终在文件末尾插入一个空行

[*.md] # 表示所有markdown文件使用一下规则
insert_final_newline = false
trim_trailing_whitespace = false
```

  `info: VSCode需要安装一个插件 EditorConfig for VS Code`

### 1.2 使用prettier工具
 Prettier是一款强大的代码格式化工具，支持JavaScript、TypeScript、CSS、SCSS、Less、JSX、Angular、Vue、GraphQL、JSON、Markdown等语言，基本上前端能用到的文件格式他都可以搞定，是当下最流行的代码格式化工具。
 1. 安装prettier
   `npm install prettier -D`
 2. 配置.prettierrc文件
   - useTabs: 使用tab缩进还是空格缩进，选择false
   - tabWidth: tab是空格的情况下，是几个空格，选择2个
   - printWidth: 当行字符的长度，推荐80
   - singleQuote: 使用单引号还是双引号，选择true，使用单引号
   - trailingComma: 在多行输入的尾逗号是否添加，设置为none
   - semi：语句末尾是否要加分号，默认值true，选择false表示不加
    ```JSON
    {
      "useTabs": false,
      "tabWidth": 2,
      "printWidth": 120,
      "singleQuote": false,
      "trailingComma": "none",
      "semi": false
    }
      
    ```  
  3. 创建.prettierignore忽略文件
   ```
   /dist/*
   .local
   .output.js
   /node_modules/**

   **/*.svg
   **/*.sh

   /public/*
   ```
  4. VSCode需要安装prettier插件
  5. 测试prettier是否生效
   - 测试一： 在代码中保存代码
   - 测试二：配置一次性修改的命令
  在package.json中配置一个script
  ```Json
  "prettier": "prettier --write ."
  ```


### 1.3 使用ESLint检测
  1. 在前面创建项目的时候，我们就选择了ESLint，所以Vue会默认帮助我们配置需要的ESLint环境
  2. VSCode需要安装ESLint插件
  3. 解决eslint和prettier冲突
     安装插件: (vue在创建项目时，如果选择prettier，那么这两个插件会自动安装)
     `npm i eslint-plugin-prettier eslint-config-prettier -D`
  添加prettier插件
  ```
  extends: [
    "plugin:vue/vue3-essential",
    "eslint:recommended",
    "@vue/typescript/recommended",
    "@vue/prettier",
    "@/vue/prettier/@typescript-eslint",
    "plugin:prettier/recommended"
  ],
  ```
  问题参考文件： 
  - `.prettier`配置文件不起作用
  https://www.pudn.com/news/62b40711dfc5ee196895e52b.html，设置vscode prettier插件中的配置文件，优先用项目中的配置文件（设置路径）
  - VSCode中ctrl+S保存是格式化代码
  https://blog.csdn.net/weixin_44292923/article/details/124316133
### 1.4 git Husky和eslint
  虽然我们已经要求项目使用eslint，但是不能保证组员在提交代码之前都将eslint中的问题解决掉了：
  - 也就是我们希望保证代码仓库中的代码都是符合eslint规范的；
  - 那么我们需要在组员执行`git commit`命令的时候对其进行 ，如果不符合eslint规范，那么自动通过规范进行修复；
  那么如何做到这一点呢？可以通过Husky工具：
  - husky是一个git hook工具，可以帮助我们触动git提交的各个阶段：pre-commit、commit-msg、pre-push
  如何使用husky呢？
  这里我们可以使用自动配置命令：
  ```js
  npx husky-init && npm install
  npx husky-init -and npm install // vscode 报错的情况下用这个
  ```
  这里会做三件事：
  1.安装husky相关的依赖
  2.在项目目录下创建`.husky`文件夹（自动加的）
  3.在package.json中添加一个脚本（自动加的）
  ```json
   {
      "script":{
        "prepare": "husky install"
      }
   }
  ```
  接下来，我们需要去完成一个操作：在进行commit时，执行lint脚本(操作这一步先把husky文件夹中pre-commit文件的执行命令 npm test 改为 npm run lint)
### 1.5 git commit 规范
 但是如果每次手动来编写这些是比较麻烦的事情，我们可以使用一个工具： Commitizen
 - Commitizen是一个帮助我们编写规范commit message的工具；
   1. 安装Commitizen
   ```js
   npm install commitizen -D
   ```
   2. 安装cz-conventional-changelog,并且初始化cz-conventional-changelog:
   
   ```js
   npx commitizen init cz-conventional-changelog --save-dev --save-exact
   ```

   这个命令会帮助我们安装cz-conventional-changelog,并且在`package.json`中进行配置
   ```json
   "config": {
      "commitizen": {
        "path": "./node_modules/cz-conventional-changelog"
      }
   }

  这个时候我们提交代码需要使用 `npx cz`,也可以在`package.json`添加脚本，通过执行脚本 `npm commit`的命令来打开commit的选择项

  ```json
   "scripts": {
      "commit": "cz"
    },
  ```

  #### 1.5.1 代码提交风格
  如果我们按照cz来规范了提交风格，但是依然有同事通过 `git commit`按照不规范的格式提交应该怎么呢？
  - 我们可以荣国commitlint来限制提交;
  1. 安装commitlint、config0conventional和@commitlint/cli
   ```js
   npm i @commitlint/config-conventional @commitlint/cli -D
   ```
   1. 在根目录创建commitlint.config.js文件，配置commitlint
   ```js
   module.exports = {
    extends: ['@commitlint/config-conventional']
   }
   ```
   1. 使用husky生成commit-msg文件，验证提交信息：
   ```js
   npx husky add .husky/commit-msg "npx --no-install commitlint --edit $1"
   ```
  #### 1.5.2 代码提交验证
    