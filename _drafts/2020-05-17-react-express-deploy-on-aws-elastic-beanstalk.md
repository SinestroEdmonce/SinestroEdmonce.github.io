---
layout: post
title: React+Express的Web应用统一部署在AWS Elastic Beanstalk上的方法
categories: [Web, React, Express, AWS, Front-end, Back-end]
description: 部署React前端+Express后端的Web应用有两种方式：一种是分离部署，通过Nginx代理服务器来实现访问；另一种是统一部署，直接在云端部署一个完整的全栈Web应用。本文主要讲解统一部署的方法和步骤。
keywords: Web, React, Express, AWS, Front-end, Back-end
---

这学期在USC学了`csci571`这门课，第8次课程项目要求用`React + Express`实现一个Web应用并且部署到云端。这个项目的部署方法基本分为两大类，一种是分离部署，通过Nginx代理服务器来实现访问；另一种是统一部署，直接在云端部署一个完整的全栈Web应用。本文主要讲解后一种方法，使用统一部署的方法将一个完整的Web应用（不含数据库）部署到`AWS Elastic Beanstalk`上。

## Web应用结构

统一部署的核心在于将`React`项目构建后的HTML文件和JS文件当作`Express`项目的资源文件。对`Express`后端进行路由访问的时候，构建好的`React`相关文件将被指定好的路由规则分发到客户端。不仅仅是`React + Express`的Web应用，其他全栈的Web应用都可以用这种统一部署的核心思路来解决。Web应用的基本结构如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/react_express_project_structure.jpg">
</div>

## 后端构建（以Nodejs的Express为例）

首先我们按照统一部署的核心思路构建项目目录结构。可以通过执行下述命令来构建一个基本的项目结构：

```bash
mkdir react-express-elb       # 项目根目录
cd react-express-elb
mkdir client                  # React前端项目存放目录
touch app.js                  # Express后端入口文件
npm init -y                   # 构建Node项目（后续安装Express包）
```

执行完上述命令后会得到一个基本的项目目录结构，如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/express_backend_setup.jpg">
</div>

其中`routes`文件夹不是必须的。`routes`文件夹负责保管所有和`Express`后端的路由规则相关的文件。打包存放这些路由规则有利于降低项目文件之间的耦合度，并且模块化项目后使得后续的维护更加方便。

## 前端构建（以React为例）

前端的构建有几种方法，一种是使用Facebook提供的`React`脚手架来创建项目：

```bash
cd client
create-react-app .
```

另一种是使用`Webpack`自己来构建`React`的项目环境。具体的`Webpack`配置方法可以参见我的另一篇博客文章[Webpack介绍和配置详解](https://sinestroedmonce.github.io/2020/05/16/webpack-intro/)。这里就不再赘述。最终形成的`React`项目的目录结构基本如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/react_frontend_setup.jpg">
</div>

其中`dist`文件夹存储了`React`项目`build`后的HTML文件（`index.html`）和JS文件（`bundle.js`），这两个文件中前者将作为页面在后端路由规则中当作`view`直接分发到客户端，后者将作为静态资源文件，在客户端（浏览器）中被请求。`src`文件夹是实际的代码文件夹，而`public`文件夹也是静态资源文件夹。

## `Express`配置和路由规则

构建完前后端的项目结构后，需要将这两者联结起来，这样才能统一部署。联结的方式其实上文也提到了，即将`React`项目相关文件当作静态资源文件或者页面文件，在`Express`中进行分发。

### 服务器基本配置

在`Express`中设置静态资源文件访问目录的代码基本如下所示：

```javascript
// Static resource path setup
server.use("/public/", express.static(path.join(__dirname, "./client/public/")));
server.use("/dist/", express.static(path.join(__dirname, "./client/dist/")));

// Set views and engines
server.set("views", path.join(__dirname, "./client/dist/"));
server.engine("html", require("ejs").renderFile);
```

这段代码将`./client/dist`和`./client/public`文件夹设置为静态资源文件夹，并且设置了URL访问路径，即只有访问`/public/xxx`时能访问到`./client/public`下的静态资源文件，只有访问`/dist/xxx`的时候能访问到`./client/dist/`下的静态资源文件。

除此之外，这段代码还设置了默认的`views`目录，即后续在制定路由规则的时候这个目录将是默认的页面文件存放位置。代码中还设置了渲染引擎，不过这个配置是可选的，只是为了后续方便写代码而已（少写一点`fs.readFile`）。

### 路由规则

在`Express`中设置的页面文件路由访问规则的代码基本如下所示：

```javascript
// Create a router instance
let router = express.Router();

// Main route for the index.html
router.get("/", function (request, response) {
    // Debug
    console.log("GET: /");
    
    response.render("index.html");
});
```

只需要直接写`response.render()`就可以从默认的`views`文件夹中将`index.html`文件拿出渲染并分发到客户端。

## 云端部署

实际部署的时候需要先将`React`项目在本地构建后（`build`完成），然后所需要的`React`项目文件仅有`dist`和`publi`下的文件，`src`下的代码文件都不需要上传到云端，即如下图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/react_express_aws_elb_structure.jpg">
</div>

在`AWS Elastic Beanstalk`上选择构建`Nodejs`环境并构建相应的应用。然后将上图所示的根目录文件夹内的内容打包（注意不是将根目录压缩，而是将根目录内的文件压缩，并且不需要压缩`node_modules`这样的第三方包库文件夹）。除此之外还需要修改一下`Express`项目的默认监听端口。在`AWS`上实际也是通过代理服务器将访问转发到监听相应端口的后端服务器上，而`AWS`默认的相应的端口记录在`process.env.PORT`中，所以代码修改如下：

```javascript
// Configure the listener port
let port = process.env.PORT || 8001;

server.listen(port, function () {
    console.log("Server is running at port 8001...");
});
```

其中`process.env.PORT`是给`AWS`使用的。自己本地的默认端口则是8001。

这样一来，直接上传压缩包文件到云端就完成部署了。

## 参考

[How to deploy an Express application with React front-end on AWS Elastic Beanstalk](https://medium.com/@wlto/how-to-deploy-an-express-application-with-react-front-end-on-aws-elastic-beanstalk-880ff7245008)