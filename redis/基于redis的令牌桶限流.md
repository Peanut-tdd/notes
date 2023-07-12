# 基于redis的令牌桶

```
<?php
namespace Api\Lib;

/**
 * 限流控制
 */
class RateLimit
{
    private $minNum = 60; //单个用户每分访问数
    private $dayNum = 10000; //单个用户每天总的访问量

    public function minLimit($uid)
{
        $minNumKey = $uid . '_minNum';
        $dayNumKey = $uid . '_dayNum';
        $resMin    = $this->getRedis($minNumKey, $this->minNum, 60);
        $resDay    = $this->getRedis($minNumKey, $this->minNum, 86400);
        if (!$resMin['status'] || !$resDay['status']) {
            exit($resMin['msg'] . $resDay['msg']);
        }
    }

    public function getRedis($key, $initNum, $expire)
{
        $nowtime  = time();
        $result   = ['status' => true, 'msg' => ''];
        $redisObj = $this->di->get('redis');
        $redis->watch($key);
        $limitVal = $redis->get($key);
        if ($limitVal) {
            $limitVal = json_decode($limitVal, true);
            $newNum   = min($initNum, ($limitVal['num'] - 1) + (($initNum / $expire) * ($nowtime - $limitVal['time'])));
            if ($newNum > 0) {
                $redisVal = json_encode(['num' => $newNum, 'time' => time()]);
            } else {
                return ['status' => false, 'msg' => '当前时刻令牌消耗完！'];
            }
        } else {
            $redisVal = json_encode(['num' => $initNum, 'time' => time()]);
        }
        $redis->multi();
        $redis->set($key, $redisVal);
        $rob_result = $redis->exec();
        if (!$rob_result) {
            $result = ['status' => false, 'msg' => '访问频次过多！'];
        }
        return $result;
    }
}

```

代码要点：

1：首先定义规则
单个用户每分钟访问次数（$minNum），单个用户每天总的访问次数（$dayNum），接口总的访问次数等不同的规则。


2：计算速率
该代码示例以秒为最小的时间单位，速率=访问次数/时间（$initNum / $expire）


3：每次访问后补充的令牌个数计算方式
获取上次访问的时间即上次存入令牌的时间，计算当前时刻与上次访问的时间差乘以速率就是此次需要补充的令牌个数，注意补充令牌后总的令牌个数不能大于初始化的令牌个数，以补充数和初始化数的最小值为准。


4：程序流程
第一次访问时初始化令牌个数（$minNum），存入Redis同时将当前的时间戳存入以便计算下次需要补充的令牌个数。第二次访问时获取剩余的令牌个数，并添加本次应该补充的令牌个数，补充后如何令牌数>0则当前访问是有效的可以访问，否则令牌使用完毕不可访问。先补充令牌再判断令牌是否>0的原因是由于还有速率这个概念即如果上次剩余的令牌为0但是本次应该补充的令牌>1那么本次依然可以访问。

