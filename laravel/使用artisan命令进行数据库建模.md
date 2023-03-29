一、建表

1、 创建migrate文件（migrate文件是用来定义mysql表的ddl文件）
```bash
php artisan make:migration create_comments_table
```

![使用artisan命令进行数据库建模](https://cdn.learnku.com/uploads/images/202303/14/66392/2O6qWXyqgC.png!large)

2、 使用php artisan migrate命令，生成数据库实体表：
```bash
php artisan migrate
```

二、创建模型

1、用命令生成model文件

```bash
php artisan make:model ActivityRateBuff
```

![](https://wdcdn.qpic.cn/MTY4ODg1NzkyNTM5MzQ3OA_718079_6x1b_4vzbFrkytGW_1678766384?w=966&h=796)

2、设置表名：

![](https://wdcdn.qpic.cn/MTY4ODg1NzkyNTM5MzQ3OA_988380_66CykzPdryZPYCTW_1678766453?w=1576&h=766)

3、执行命令生成model注释

```bash
php artisan ide-helper:models App\\Models\\ActivityRateBuff -W
```

![](https://wdcdn.qpic.cn/MTY4ODg1NzkyNTM5MzQ3OA_125975_MyQB0N9EtHulGAQ6_1678766506?w=2098&h=988)