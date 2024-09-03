## pipeline使用demo

```go
package cache

import (
	"fmt"
	"github.com/spf13/cast"
	"gitlab.yx/base-service/go-zero/core/stores/redis"
	"ignore_question/common/constants"
	"time"
)




func GetCacheQuestionIds(cc *CacheData, cacheKey string, startTimeStamp, currentTimeStamp int64, questionIds []string) (cacheQuestion []CacheQuestions) {

	//pipeline 调用zscore
	sliLen := 50 //50条数据一个查询批次
	sliceQuestionIds := make([]string, 0)
	result := []*redis.FloatCmd{}
	_ = cc.redisClient.Pipelined(func(pipeliner redis.Pipeliner) error {

		for i := 0; i < len(questionIds); i += sliLen {

			end := i + sliLen
			if end > len(questionIds) {
				end = len(questionIds)
			}
			sliceQuestionIds = questionIds[i:end]
			for _, questionId := range sliceQuestionIds {
				res := pipeliner.ZScore(cc.ctx, cacheKey, questionId)
				result = append(result, res)
			}
		}

		return nil
	})

	for _, cmd := range result {
		questionId := cmd.Args()[2]      //questionId
		score := cast.ToInt64(cmd.Val()) //score
		if score != 0 && score >= startTimeStamp && score <= currentTimeStamp {
			cacheQuestion = append(cacheQuestion, CacheQuestions{
				Key: cast.ToString(questionId),
			})
		}
	}
	return
}

```



