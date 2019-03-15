> 前言：官方文档在这方面还是没有讲清楚什么使用，怎么应用，只是给了个大概的配置和大致的使用方式，限制在单数据流缓存get请求的数据上面，并没有实质意义上用到缓存的处理。于是博主花了接近一个星期翻看源码以及逐步测试，发现实际上使用真的很简单，这里确实要夸一下nestjs的封装。接下来博主就以验证码缓存来着重介绍其用法。

### 1、源码分析：
通过官方文档，我们知道要写个拦截器继承CacheInterceptor进行对服务的交互处理，于是让我们查看一下它做了什么操作：
```javascript
let CacheInterceptor = class CacheInterceptor {
    constructor(cacheManager, reflector) {
        this.cacheManager = cacheManager;
        this.reflector = reflector;
    }
    async intercept(context, call$) {
        const key = this.trackBy(context);
        if (!key) {
            return call$;
        }
        try {
            const value = await this.cacheManager.get(key);
            if (value) {
                return rxjs_1.of(value);
            }
            return call$.pipe(operators_1.tap(response => this.cacheManager.set(key, response)));
        }
        catch (_a) {
            return call$;
        }
    }
    trackBy(context) {
        const httpServer = this.applicationRefHost.applicationRef;
        const isHttpApp = httpServer && !!httpServer.getRequestMethod;
        if (!isHttpApp) {
            return this.reflector.get(cache_constants_1.CACHE_KEY_METADATA, context.getHandler());
        }
        const request = context.getArgByIndex(0);
        if (httpServer.getRequestMethod(request) !== 'GET') {
            return undefined;
        }
        return httpServer.getRequestUrl(request);
    }
};
```

#### 1.1、trackBy()
此方法传递一个context上下文进来，通过上下文获取我们请求的对象，如果是get请求，则找到此时在请求阶段promise的CacheKey的值；如果是post或其他请求方式则不做操作。

#### 1.2、intercept()
此方法对服务端回数据制做进一步的处理，通过rxjs的分发机制，我们可以了解到，它分发了我们此次请求回来的response数据，同时也通过构造中的cacheManager来保存了当前的response。更为重要的是根据它自定义的trackBy方法获取了之前的key值，这点很关键。我们后面获取数据时的必要条件。

### 2、自定义拦截器：

#### 2.1、需求
> 在我们开始编程的时候必须先理清楚我们需求的功能，我们需要缓存此次的验证码，并且通过key再次获取它进行进一步的验证。

#### 2.2、分别建立两个拦截器：

**PutCodeInterceptor**
```typescript
@Injectable()
export class PutCodeInterceptor extends CacheInterceptor {
   trackBy(context: ExecutionContext) {
     const req = context.switchToHttp().getRequest();
     return 'smsKey';
   }
}
```

**GetCodeInterceptor**
```typescript
@Injectable()
export class GetCodeInterceptor extends CacheInterceptor {
  async intercept(context: ExecutionContext, call$: Observable<any>): Promise<Observable<any>> {
    const result = await this.cacheManager.get('smsKey');
    return call$.pipe(map(data => ({ result })));
  }
}
```

> **分析：** PutCodeInterceptor 将key值通过request来进行获取，这里我简单的写死为'smsKey'，这样便可以通过CacheIntercepter的 intercept方法获取此key来根据key缓存response。 然后作为再次获取此缓存的value ，再次添加GetCodeInterceptor 重写 CacheIntercepter 的 intercept方法 ，最终通过cacheManager的get()方法获取方才缓存的response，最终可以通过缓存的response来进行判断处理，我这里为了方便起见直接当result返回了。

### 3、使用：

```typescript
@Controller('sms')
export class SmsController{
    constructor(private log: Log, private app: AppService) { }
    @Get('put')
    @UseInterceptors(PutCodeInterceptor)
    async testCode() {
        const rep: DoctorInfo = {
            name: '你好',
            code: this.app.createCode(),
        };
        return rep;
    }

    @Post('test')
    @UseInterceptors(GetCodeInterceptor)
    async test() {
    }

}
```

最终测试效果：

**put：**

![put.png](https://upload-images.jianshu.io/upload_images/4253553-f2aeb021fc396407.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**test：**

![test.png](https://upload-images.jianshu.io/upload_images/4253553-ba1e519cc5f1d17e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
*provider by stormKid*
*原文链接：https://www.jianshu.com/p/e7b0f3eb3aed*