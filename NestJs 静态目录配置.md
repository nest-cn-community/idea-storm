> 网上查看了很多文档，发现很多都是自己实现中间件来完成此功能，不仅浪费时间，而且增加了太多的代码量。实际上，nest已经帮助我们封装好了相关功能。

### 1、查找线索
由于官方文档没有做详细解释说明，那么我们可以从此框架底层入手：
**我们知道，nestjs底层用的是express，那么express是通过什么来完成静态目录构建的：**
>**serve-static**

### 2、搜索源码
我们在项目搜索栏目中搜索“serve-static”会发现如下图：
![yarn.lock.png](https://upload-images.jianshu.io/upload_images/4253553-2009c02ddd20f1c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也就是说，当我们在使用nest框架的时候，serve-static 会随之而构建好，那么我们直接参考其源码即可：**[依赖地址](https://github.com/expressjs/serve-static)**
Nestjs 源码：
```@typescript
   // Type definitions for serve-static 1.13
// Project: https://github.com/expressjs/serve-static
// Definitions by: Uros Smolnik <https://github.com/urossmolnik>
//                 Linus Unnebäck <https://github.com/LinusU>
// Definitions: https://github.com/DefinitelyTyped/DefinitelyTyped
// TypeScript Version: 2.2

/* =================== USAGE ===================

    import * as serveStatic from "serve-static";
    app.use(serveStatic("public/ftp", {"index": ["default.html", "default.htm"]}))

 =============================================== */

/// <reference types="express-serve-static-core" />

import * as express from "express-serve-static-core";
import * as m from "mime";

/**
 * Create a new middleware function to serve files from within a given root directory.
 * The file to serve will be determined by combining req.url with the provided root directory.
 * When a file is not found, instead of sending a 404 response, this module will instead call next() to move on to the next middleware, allowing for stacking and fall-backs.
 */
declare function serveStatic(root: string, options?: serveStatic.ServeStaticOptions): express.Handler;

declare namespace serveStatic {
    var mime: typeof m;
    interface ServeStaticOptions {
        /**
         * Enable or disable setting Cache-Control response header, defaults to true. 
         * Disabling this will ignore the immutable and maxAge options.
         */
        cacheControl?: boolean;

        /**
         * Set how "dotfiles" are treated when encountered. A dotfile is a file or directory that begins with a dot (".").
         * Note this check is done on the path itself without checking if the path actually exists on the disk.
         * If root is specified, only the dotfiles above the root are checked (i.e. the root itself can be within a dotfile when when set to "deny").
         * The default value is 'ignore'.
         * 'allow' No special treatment for dotfiles
         * 'deny' Send a 403 for any request for a dotfile
         * 'ignore' Pretend like the dotfile does not exist and call next()
         */
        dotfiles?: string;

        /**
         * Enable or disable etag generation, defaults to true.
         */
        etag?: boolean;

        /**
         * Set file extension fallbacks. When set, if a file is not found, the given extensions will be added to the file name and search for.
         * The first that exists will be served. Example: ['html', 'htm'].
         * The default value is false.
         */
        extensions?: string[];

        /**
         * Let client errors fall-through as unhandled requests, otherwise forward a client error.
         * The default value is false.
         */
        fallthrough?: boolean;

        /**
         * Enable or disable the immutable directive in the Cache-Control response header.
         * If enabled, the maxAge option should also be specified to enable caching. The immutable directive will prevent supported clients from making conditional requests during the life of the maxAge option to check if the file has changed.
         */
        immutable?: boolean;

        /**
         * By default this module will send "index.html" files in response to a request on a directory.
         * To disable this set false or to supply a new index pass a string or an array in preferred order.
         */
        index?: boolean | string | string[];

        /**
         * Enable or disable Last-Modified header, defaults to true. Uses the file system's last modified value.
         */
        lastModified?: boolean;

        /**
         * Provide a max-age in milliseconds for http caching, defaults to 0. This can also be a string accepted by the ms module.
         */
        maxAge?: number | string;

        /**
         * Redirect to trailing "/" when the pathname is a dir. Defaults to true.
         */
        redirect?: boolean;

        /**
         * Function to set custom headers on response. Alterations to the headers need to occur synchronously.
         * The function is called as fn(res, path, stat), where the arguments are:
         * res the response object
         * path the file path that is being sent
         * stat the stat object of the file that is being sent
         */
        setHeaders?: (res: express.Response, path: string, stat: any) => any;
    }
    function serveStatic(root: string, options?: ServeStaticOptions): express.Handler;
}

export = serveStatic;

```
### 3、使用方式：
>说明：源码中的注释说的很清楚用法，由于现阶段技术有限，博主将项目目录作为文件地址来简单的使用。

**代码使用：只需要一句代码：**
在 main.ts文件中：
```@typescript
    //...
    import * as serveStatic from 'serve-static';
    async function bootstrap() {
    const app = await NestFactory.create(AppModule);
    //....
    // 使用serve-static
    // '/public' 是路由名称，即你访问的路径为：host/public
    // serveStatic 为 serve-static 导入的中间件，其中'../public' 为本项目相对于src目录的绝对地址
    app.use('/public', serveStatic(path.join(__dirname, '../public'), {
     maxAge: '1d',
     extensions: ['jpg', 'jpeg', 'png', 'gif'],
    }));
    //....
    await app.startAllMicroservicesAsync();
    await app.listen(9871);
}
bootstrap();

```

**在项目根目录下创建public目录：**
![目录创建.png](https://upload-images.jianshu.io/upload_images/4253553-5d4d2203cb6eef82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 4、测试效果：
首先使用nestjs自带的upload api来上传文件，这里不做过多说明，最终通过postman完成测试文件上传：

![测试上传.png](https://upload-images.jianshu.io/upload_images/4253553-5b2498b7fa8aa1e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再使用浏览器浏览：
![浏览图片.gif](https://upload-images.jianshu.io/upload_images/4253553-17d888a490050d85.gif?imageMogr2/auto-orient/strip)


---
*provider by stormKid*
*原文链接：https://www.jianshu.com/p/d10134dae4cc*