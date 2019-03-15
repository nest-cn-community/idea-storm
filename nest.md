# nest #
## nest 简介 ##
Nest是一个用于构建高效，可扩展的Node.js服务器端应用程序的框架。使用TypeScript（JavaScript的超集）构建（保留与纯JavaScript的兼容性），并结合了OOP（面向对象编程），FP（功能编程）和FRP（功能反应编程）的元素。
Nest提供了开箱即用的应用程序架构，可以轻松创建高度可测试，可扩展，松散耦合且易于维护的应用程序。

## 环境准备 ##

查看nodejs:在终端中输入node -v 回车查看（vsCode设置问题，所以图片中看不到 -v ）

![](https://i.imgur.com/ojtyQvj.png)

#### 注意 ：nestjs需要 Node.js 版本 >= 6.11.0

## nestjs搭建 :
在终端中使用npm/cnpm 安装nestjs  cli工具   npm i  @nestjs/cli

![](https://i.imgur.com/KCmukUT.png)

### 查看是否安装成功
![](https://i.imgur.com/We4XAzJ.png)

出现如图所示，则证明安装成功（依次显示的是：系统版本；nodejs版本；npm版本）
### 查看nest版本
使用nest --version命令查看nest当前版本:

![](https://i.imgur.com/hhBBjsd.png)

##新建项目
选择好工作目录后，在终端输入：nest new project(项目名)

![](https://i.imgur.com/JM01gLx.png)

![](https://i.imgur.com/G6S4bL0.png)

我选择的是npm 接下来一般我会选择终止，不然会等待很久，终止了也不会有影响
###项目结构：
（暂时忽略报错信息，下面会解决）

![](https://i.imgur.com/QpWH6FM.png)

会java 的和angular的大大们有没有发现项目结构有点眼熟，作为一个被强迫从java转到nest的我来说看到的第一眼感觉亲近了很多。
####src目录中包含下面几个核心文件：
#####app.controller.ts    // 带有单个路由的基本控制器示例。
#####app.module.ts    // 定义 AppModule 应用程序的根模块。
#####main.ts是项目的入口文件, 定义了一个异步方法(bootstrap)来启动应用，通过 NestFactory 工厂类的 create 方法，创建一个实现了 INestApplication 接口的服务实例：默认监听端口3000（如图）
![](https://i.imgur.com/Ivzxn3j.png)

main.ts   我做了如下修改,可以清楚的看到是否启动以及监控到启动端口号，这样在做微服务的时候不会导致端口重复使用
![](https://i.imgur.com/k9UId0S.png)

##安装依赖包
上面两张图中的报红相信各位大大看的都很不舒服，在终端中使用 **npm i**  来安装依赖包 就会发现错误去流浪了

##启动项目 
使用**npm start** 来启动，默认的端口号是3000，我的是3001

在浏览器中访问**http://localhost:3001/** ，能够看到输出 Hello world!

![](https://i.imgur.com/HI2qvbd.png)
