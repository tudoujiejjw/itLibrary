# 获取openid

## 一、前置条件

### 1、公众号必须为服务号
### 2、公众号完成了企业认证
### 3、服务域名加入白名单
### 4、服务器ip加入白名单


## 二、获取code
https://open.weixin.qq.com/connect/oauth2/authorize?appid=wxddfb84ab8320beb3&redirect_uri=http%3A%2F%2Fuat.jianleyunfu.com%2Fm%2F&response_type=code&scope=snsapi_base#wechat_redirect


### 在跳转回来的地址上回带有code

```php
$code = $_GET['code''];
```

### 三、获取openid
```php
        $appid = config('weixin.jlyf_appid');
        $secret = config('weixin.jlyf_secret');
        $code = $request['code'];//获取code
        //通过code换取网页授权access_token
        $url = "https://api.weixin.qq.com/sns/oauth2/access_token?appid=$appid&secret=$secret&code=$code&grant_type=authorization_code";
        $weixin = file_get_contents($url);
        $jsondecode = json_decode($weixin); //对JSON格式的字符串进行编码
        $array = get_object_vars($jsondecode);//转换成数组
        if (empty($array['openid'])) {
            \Log::channel('request')->warning('openid获取失败', [
                'url' => $url,
                'result' => $weixin,
                'user_id' => $uid
            ]);
            return $this->jsonError('获取openid失败');
        }

        //获取用户的openid清单，如果没有绑定关系，则进行绑定
        $openid = $array['openid'];

```