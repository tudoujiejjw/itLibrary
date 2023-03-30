# 数据操作层Repositories接口设计
## 前言
> 许多程序员，由于框架对数据库操作的便利性、不对数据操作层进行封装，
> 有的把数据库操作写在service层，也有的直接写在了controller，
> 甚至各个脚本文件中。 这实际上并不利于软件设计的"复用性"、"封装性"和"统一性"；


> 另一方面，对数据库操作类的方法命名有许多不规范的地方，这一现象导致了数据操作层（Repositories）
> 代码的复用率并没有得到很大的提升。

> 本次设计的目标：<br>
> 1、建立数据操作层——Repositories；<br>
> 2、设计Interface接口，限制常用方法的统一性、合法性；<br>
> 3、不将字段声明、where语句暴露在Repositories之外，数据库操作相关的语句一律封装在Repositories之中。


## 一、设计Interface

```php
namespace App\Repositories;

interface RepositoryInterface {
    //获取所有数据
    //$conditions：一般会带一些查询条件，查询条件在conditions中描述；
    //$columns：指定字段可以不传，默认的$columns字段，可以在具体实现中去指定。如遇不需要那么多字段的情况下，也允许使用者指定
    static public function all(array $conditions = [], array $columns = ['*']);
    
    //获取分页数据
    //$perPage：每页数据量，可以指定为（int），也可以传null，具体实现中，遇到null可以设置一个默认的数量；
    //$pageNo：页码，可以指定为（int），也可以传null，具体实现中，遇到null可以设置一个默认的数量；
    //$conditions：一般会带一些查询条件，查询条件在conditions中描述；
    //$columns：指定字段可以不传，默认的$columns字段，可以在具体实现中去指定。如遇不需要那么多字段的情况下，也允许使用者指定
    static public function paginate(int|null $perPage, int|null $pageNo, array $conditions = [], array $columns = []);
    
    //写入一条数据（如果需要批量写入可以设计一个batchCreate
    //$data：一维数组写入
    static public function create(array $data);
    
    //更新一条数据（如果需要批量写入可以设计一个batchUpdate
    //$data：一维数组更新
    //$id：主键id
    static public function update(array $data, int $id);
    
    //删除一条数据
    //$id：主键id
    static public function delete(int $id);
    
    //查找一条数据
    //$id：主键id
    //$columns：指定字段可以不传，默认的$columns字段，可以在具体实现中去指定。如遇不需要那么多字段的情况下，也允许使用者指定
    static public function find(int $id, $columns = ['*']);
    
    //通过指定字段查询一条数据
    //$field：字段名
    //$value：查找值
    //$columns：指定字段可以不传，默认的$columns字段，可以在具体实现中去指定。如遇不需要那么多字段的情况下，也允许使用者指定
    static public function findBy(string $field, $value, array $columns = ['*']);
    
    //根据一组查询条件查询一条数据
    //$conditions：复杂的查询条件
    //$columns：指定字段可以不传，默认的$columns字段，可以在具体实现中去指定。如遇不需要那么多字段的情况下，也允许使用者指定
    static public function findByConditions(array $conditions, array $columns = ['*']);
}
```


## 二、实现接口
```php
namespace App\Repositories;

use App\Models\WxMsg;

class WxMsgRepository implements RepositoryInterface
{
    static public function all(array $columns = ['*'], array $conditions = []){}

    /**
     * @description:获取消息分页数据
     * @auther: guoqingfeng
     * @Date: 2023/3/23
     * @param int|null $perPage  每页数量
     * @param int|null $pageNo   页码
     * @param array $conditions 查询条件
     * @param array $columns    自定义字段
     * @return \Illuminate\Contracts\Pagination\LengthAwarePaginator
     */
    static public function paginate(int|null $perPage, int|null $pageNo, array $conditions = [], array $columns = [])
    {
        $columns = $columns ?: ['id', 'title', 'type', 'created_at'];
        $query = WxMsg::query();
        $query->orderByDesc('id');
        return $query->paginate($perPage ?? 10, $columns, 'pageNo', $pageNo ?? 1);
    }

    /**
     * @description: 创建一条记录
     * @auther: guoqingfeng
     * @Date: 2023/3/21
     * @param array $data
     * @return bool
     */
    static public function create(array $data)
    {
        if (empty($data)) {
            return false;
        }
        $query = WxMsg::query();
        return $query->insert($data);
    }

    /**
     * @description: 创建一条记录并返回id
     * @auther: guoqingfeng
     * @Date: 2023/3/27
     * @param array $data
     * @return false|int
     */
    static public function createGetId(array $data)
    {
        if (empty($data)) {
            return false;
        }
        $query = WxMsg::query();
        return $query->insertGetId($data);
    }

    static public function update(array $data, int $id){}
    static public function delete(int $id){}
    static public function find(int $id, $columns = ['*']){}
    static public function findBy(string $field, $value, array $columns = ['*']){}
    static public function findByConditions(array $conditions, array $columns = ['*']){}
}

```