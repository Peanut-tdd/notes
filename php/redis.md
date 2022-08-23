# Reids使用方法

```php
<?php

namespace App\Components;


/**
 * Redis操作类
 * @author wancheng
 */
class Redis
{
    private static $__redis;

    public static function getRedis()
    {
        $server = [
            'host' => env('REDIS_HOST'),
            'port' => env('REDIS_PORT'),
            'database' => env('REDIS_DB'),
            'password'=>env('REDIS_PASSWORD')
        ];

        if (!isset(self::$__redis)) {
            self::$__redis = new \Predis\Client($server);
        }
        return self::$__redis;
    }


    /**
     * 设置 String
     * @param [string] $key 键名
     * @param [fixed] $value 值 字符串/数组
     * @param [int] $expire 过期时间
     */
    public static function set($key, $value, $expire = 0)
    {
        if (is_array($value) OR is_object($value)) {
            $value = json_encode($value);
        }
        $result = self::getRedis()->set($key, $value);
        if (!empty($expire)) {
            self::getRedis()->expire($key, $expire);
        }
        return $result;
    }

    /**
     * 设置 String,如果已存在就不设置，返回false
     * @param [string] $key 键名
     * @param [fixed] $value 值 字符串/数组
     * @return bool true-成功，false-失败
     */
    public static function setnx($key, $value = 1)
    {
        if (is_array($value) OR is_object($value)) {
            $value = json_encode($value);
        }
        $result = self::getRedis()->setnx($key, $value);
        return $result;
    }

    /**
     * 设置 string,如果已存在就覆盖旧值 原子锁
     * @DateTime    2019-08-14
     * @param [string] $key 键名
     * @param [fixed] $value 值 字符串/数组
     * @param [integer]    $time  过期时间
     * @return      bool true-成功，false-失败
     */
    public static function setex($key, $value = 1, $time = 5)
    {
        if (is_array($value) OR is_object($value)) {
            $value = json_encode($value);
        }
        $result = self::getRedis()->setex($key, $time, $value);
        return $result;
    }

    /**
     * 获取 String
     * @param [string] $key 键名
     * @return [string]
     */
    public static function get($key)
    {
        $value = self::getRedis()->get($key);
        $result = json_decode($value, true);
        return is_null($result) ? $value : $result;
    }

    /**
     * 删除str
     * @param [string] $field_key 字符串为键名，hash为字段名，
     * @return [bool]
     */
    public static function del($key)
    {
        return self::getRedis()->del($key);
    }


    /**
     * 设置 hash
     * @param [string] $field 字段名
     * @param [string] $key 键名
     * @param [fixed] $value 值 hash
     * @return [bool]
     */
    public static function hset($field, $key, $value)
    {
        if (is_array($value) || is_object($value)) {
            $value = json_encode($value);
        }
        return self::getRedis()->hset($field, $key, $value);
    }

    /**
     * 获取 hash
     * @param [string] $field 字段名
     * @param [string] $key 键名
     * @return [bool]
     */
    public static function hget($field, $key = '')
    {
        if (empty($key)) {
            $values = self::getRedis()->hgetall($field);
            $out = array();
            foreach ($values as $key => $value) {
                if ($key % 2 == 1) {
                    $out[$values[$key - 1]] = !is_null(json_decode($values[$key], true)) ? json_decode($values[$key], true) : $values[$key];
                }
            }
            return $out;
        } else {
            $values = self::getRedis()->hget($field, $key);
            return !is_null(json_decode($values, true)) ? json_decode($values, true) : $values;
        }
    }

    /**
     * 获取 hash
     * @param [string] $field 字段名
     * @param [string] $key 键名
     * @return [bool]
     */
    public static function hgetv2($field, $key = '')
    {
        if (empty($key)) {
            $values = self::getRedis()->hvals($field);
            foreach ($values as $key => &$value) {
                $value = !is_null(json_decode($value, true)) ? json_decode($value, true) : $value;
            }
            return $values;
        } else {
            $values = self::getRedis()->hget($field, $key);
            return !is_null(json_decode($values, true)) ? json_decode($values, true) : $values;
        }
    }

    /**
     * 判断hash key是否存在
     * @param  [type]
     * @param  string
     * @return [type]  [description]
     */
    public static function hexists($field, $key = '')
    {
        return !empty(self::getRedis()->hexists($field, $key)) ? true : false;
    }

    /**
     * 批量设置hash值
     * @version 1.2
     * @param  [string] $field 字段
     * @param  array $values 关联数组 ex:array(key1=>$value1,$key2=>$value2,...)
     * @return [type]  [description]
     */
    public static function hmset($field, $values = array())
    {
        return self::getRedis()->hmset($field, $values);
    }

    /**
     * 批量获取hash值
     * @param  [type] $field 字段
     * @param  array $values 索引数组 ex:array(key1,key2,...)
     * @return [type]  [description]
     */
    public static function hmget($field, $values = array())
    {
        return self::getRedis()->hmget($field, $values);
    }

    /**
     * 获取名称为xx的所有值
     * @param  [string] $field
     * @return [type]  [description]
     */
    public static function hvals($field)
    {
        return self::getRedis()->hvals($field);
    }

    /**
     * 获取hash的key总数
     * @param [string] $key 表名
     * @return [string]
     */
    public static function hlen($key)
    {
        return self::getRedis()->hlen($key);
    }

    /**
     * 删除hash
     * @param [string] $field 字段名，
     * @param [string] $key hash的key值
     * @return [string]
     */
    public static function hdel($field, $key = '')
    {
        return self::getRedis()->hdel($field, $key);
    }

    /**
     * 设置 List
     * @param [string] $field LIST名
     * @param [array] $value 列表内容（左右顺序有对调）
     * @return [bool]
     */
    public static function lpush($field, $value)
    {
        if (is_array($value) || is_object($value)) {
            $value = json_encode($value);
        }
        self::getRedis()->lpush($field, $value);
        return true;
    }

    /**
     * 修改 List
     * @param [string] $field LIST名
     * @param [array] $key 位置key
     * @param [array] $value 修改的值
     * @return [bool]
     */
    public static function lset($field, $key, $value)
    {
        if (is_array($value) || is_object($value)) {
            $value = json_encode($value);
        }
        return self::getRedis()->lset($field, $key, $value);
    }


    /**
     * 获取 List
     * @param [string] $field list名字
     * @param [int] $start 开始位置 默认从0开始
     * @param [int] $end 结束位置 默认加载全部
     * @return [string]
     */
    public static function lget($field, $start = 0, $end = true)
    {
        $length = self::getRedis()->llen($field);
        if (is_numeric($end)) {
            $values = self::getRedis()->lrange($field, $start, $end);
        } else {
            $values = self::getRedis()->lrange($field, $start, $length);
        }

        foreach ($values as $key => $value) {
            $values[$key] = !is_null(json_decode($value, true)) ? json_decode($value, true) : $value;
        }
        return $values;
    }


    /**
     * 删除
     * @param [string] $key 表名
     * @param [string] $value 值
     * @param [string] $count 位置
     * @return [string]
     */
    public static function ldel($key, $value, $count)
    {
        return self::getRedis()->lrem($key, $value, $count);
    }

    /**
     * 输出名称为key的list长度
     * @param [string] $key 表名
     * @return [bool]
     */
    public static function llen($key)
    {
        return self::getRedis()->llen($key);
    }

    /**
     * 输出名称为key的list左(头)起的第一个元素，删除该元素
     * @param [string] $key 表名
     * @return [bool]
     */
    public static function lpop($key)
    {
        $value = self::getRedis()->lpop($key);
        return !is_null(json_decode($value, true)) ? json_decode($value, true) : $value;
    }

    /**
     * 输出名称为key的list右（尾）起的第一个元素，删除该元素
     * @param [string] $key 表名
     * @return [bool]
     */
    public static function rpop($key)
    {
        $value = self::getRedis()->rpop($key);
        return !is_null(json_decode($value, true)) ? json_decode($value, true) : $value;
    }


    /**
     * 向名称为key的set中添加元素value,如果value存在，不写入，return false
     * @param [string] $key 表名
     * @param [string] $value 值
     * @return [type] [description]
     */
    public static function sadd($key, $value)
    {
        return self::getRedis()->sadd($key, $value);
    }

    /**
     * 判断元素是否属于当前set集合
     * @param [string] $key 表名
     * @param [string] $value 值
     * @return [type] [description]
     */
    public static function sismember($key, $value)
    {
        return self::getRedis()->sismember($key, $value);
    }

    /**
     * 删除名称为key的set中的元素value
     * @param [string] $key 表名
     * @param [string] $value 值
     * @return [type] [description]
     */
    public static function srem($key, $value)
    {
        return self::getRedis()->srem($key, $value);
    }

    /**
     * 指定值自增
     * @param  [string]  $key
     * @param  integer $value 自减的值
     * @param  integer $expire 过期时间，默认为0不过期
     * @return [type]   [description]
     */
    public static function incr($key, $value = 0, $expire = 0)
    {
        if ($value > 0) {
            $result = self::getRedis()->incrBy($key, $value);
        } else {
            $result = self::getRedis()->incr($key);
        }
        if (!empty($expire)) {
            self::getRedis()->expire($key, $expire);
        }
        return $result;
    }


    public static function expire($key, $expire)
    {
        return self::getRedis()->expire($key, $expire);
    }


    /**
     * redis 持续锁,必须最少锁定时长
     * @param $func
     * @param int $sec
     * @param array ...$keys
     * @return bool
     */
    public static function lock_after($func, $sec = 30, ...$keys)
    {
        $key = implode("", $keys);
        $pkey_lock = "synchronized:lock_after:{$key}";
        $locknum = self::incr($pkey_lock);
        if ($locknum == 1) {
            /*默认最少锁定30s*/
            self::expire($pkey_lock, $sec);
            return $func();
        } else {
            return true;
        }
    }

}
```

