# redis安装及其使用

## 可视化工具使用

### qishibo/AnotherRedisDesktopManager
>
> 地址：https://gitee.com/qishibo/AnotherRedisDesktopManager/releases
> 


## PHP与redis
```php
use Illuminate\Support\Facades\Redis;

//选择数据库
Redis::select(10);

//设置一个变量
Redis::set('wx_access_token', $ticketRes['access_token']);

//设置一个过期变量
Redis::setex('wx_access_token', 7200, $ticketRes['access_token']);

//获取一个变量
Redis::get('wx_access_token');

```