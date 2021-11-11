# HTTP Kernel Handle 解析

1. 构造方法内注入`Illuminate\Foundation\Application`应用容器和`Illuminate\Routing\Router`路由

2. 将$middlewareGroups、$routeMiddleware、$middlewarePriority相关路由配置信息赋值给路由，构造方法执行完毕

3. 执行handle方法

   ​	1.调用`Illuminate\Foundation\Http\Kernel` 的 sendRequestThroughRouter方法将$request注入`Illuminate\Foundation\Application`应用容器

   ​	2.Bootstrap 解析,依次执行

   - `\Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables`

   - `\Illuminate\Foundation\Bootstrap\LoadConfiguration`

   - `\Illuminate\Foundation\Bootstrap\HandleExceptions`

   - `\Illuminate\Foundation\Bootstrap\RegisterFacades`

   - `\Illuminate\Foundation\Bootstrap\RegisterProviders`

   - `\Illuminate\Foundation\Bootstrap\BootProviders`

     这些bootstrap类的bootstrap方法

     | 类                                                          | 作用           |
     | :---------------------------------------------------------- | -------------- |
     | `\Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables` | 加载环境变量   |
     | `\Illuminate\Foundation\Bootstrap\LoadConfiguration`        | 加载config     |
     | `\Illuminate\Foundation\Bootstrap\HandleExceptions`         | 错误处理者     |
     | `\Illuminate\Foundation\Bootstrap\RegisterFacades`          | 注册门面类     |
     | `\Illuminate\Foundation\Bootstrap\RegisterProviders`        | 注册服务提供者 |
     | `\Illuminate\Foundation\Bootstrap\BootProviders`            | 启动服务提供者 |

     

   4.调用管道Pipeline，在进入管道前， 调用了 `dispatchToRouter` 返回一个闭包对象，匹配路由的逻辑清晰可见，如果一路抽丝剥茧，我们便能找到 Router 调用 controller 的逻辑了

   

