# Laravel 错误日志的插件 (Laravel-log-viewer)

## 代码 : https://github.com/rap2hpoutre

## 安装

```shell
composer require rap2hpoutre/laravel-log-viewer
```


## 配置
### config/app.php 的 provider
```php
Rap2hpoutre\LaravelLogViewer\LaravelLogViewerServiceProvider::class,
```


## 路由
```php
Route::get('logs', '\Rap2hpoutre\LaravelLogViewer\LogViewerController@index');
```