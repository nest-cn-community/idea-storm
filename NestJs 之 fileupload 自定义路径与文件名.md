>在写nest项目的时候，写到fileupload 这段时，根据官方文档，发现，上传过来的文件全部都变成了一串加密的编码，例如：
![加密编码图示.png](https://upload-images.jianshu.io/upload_images/4253553-4003bebd9c411a8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>于是本来在issue中希望能够找到解决方法，但是完全没办法解决这类问题。于是博主开始翻阅了nestJS的源码。

![FileInterceptor 的 MulterOptions 源码.png](https://upload-images.jianshu.io/upload_images/4253553-a86647cfd3e35e04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**这里我们知道了，nest.js 使用的是multer 来封装的，所以我们可以直接使用multer类来进行自定义处理**

根据此github 文档，我们可以直接在uploadController中书写：
```typescript
import { Controller, Post, UseInterceptors, UploadedFile, FileInterceptor} from '@nestjs/common';
import multer = require('multer');
@Controller('upload')
export class UploadController {
    @Post()
    @UseInterceptors(FileInterceptor('file', {
        storage: multer.diskStorage({
            destination: (req, file, cb) => {
                cb(null, 'c:/Users/ke_li/Desktop/test/');
            },
            filename: (req, file, cb) => {
                cb(null, file.originalname);
            },
        }),
    }))
    async uploade(@UploadedFile() file) {
        return file;
    }
}
```

>**说明：destination类似于option字段 desk，指定uploadfile的目录，filename则是当前upload的file给予指定文件的文件名称， file.originalname 则是 file 在本地的文件名**

**于是我们获得了以下请求：**
![postman 请求.png](https://upload-images.jianshu.io/upload_images/4253553-9d4776790e1751b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**文件上传的目录**
![上传的文件.png](https://upload-images.jianshu.io/upload_images/4253553-ebc012d9bd9f9dea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样就完成了我们对文件目录及名称的自定义。

