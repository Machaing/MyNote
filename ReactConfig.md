

## 1. 基于webpack4的Redux自定义配置

### 1.1 根目录下创建文件夹

```
/src
	/index.html
	/index.js 
/dist
/.babelrc
```



### 1.2 安装命令

```js
// webpack
npm init
cnpm i webpack webpack-cli -D （默认cnpm已经配置好）
cnpm i webpack -D （默认cnpm已经配置好）
cnpm i webpack-dev-server -D
cnpm i html-webpack-plugin -D

//babel
cnpm i babel-core babel-loader babel-plugin-transform-runtime -D
cnpm i babel-preset-env babel-preset-stage-0 -D
cnpm i babel-preset-react -D

// react
cnpm i react react-dom -S
```



### 1.3 各个配置文件

```

```



### 1.4 各个常用插件安装以及配置

#### 1.4.1 eslint

#### 1.4.2 axios

#### 1.4.3 ui



## 2.React Cli

### 2.1 Create React App

#### 2.1.1 官网

https://www.html.cn/create-react-app/docs/getting-started/

#### 2.1.2 常用命令

```cmd
# 1.项目安装
# 安装 Create React App 脚手架
cnpm install -g create-react-app
create-react-app [proName]
# npx Create React App 脚手架
npx create-react-app my-app

# 2.运行项目
npm start

# 3.交互模式运行
npm test
```



## 3. 解决Cli下载各个包慢的问题

替换npm下载源即可：

```cmd
官方:https://registry.npm.taobao.org
阿里:https://registry.npm.taobao.org

更改操作：
npm config set registry https://registry.npm.taobao.org

查看操作：
npm config get registry
```

​	

## 4. VSCode配置

1. react snippets 最多下载数即可

   ```
   1.有状态组件
   rcc
   2.无状态组件
   rfc
   ```

2. 