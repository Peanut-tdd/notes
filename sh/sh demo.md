## sh demo

```
#!/bin/sh

RUNNING_PATH=$(dirname $0)
cd $RUNNING_PATH

PHP_BIN=/usr/local/php/bin/php
DEAMON_BIN=kafkaworker.php
LOG_FILE=/tmp/kafkaworker_monitor.log

moni_worker()
{
  taskname=$1
  proc_def_num=$2
  proc_num=`ps aux|grep -w "$DEAMON_BIN $taskname" |grep -v "grep" -c`

  time_m=`date '+%F %T'`
  diff=`expr $proc_def_num - $proc_num`;

  if [ $proc_num -lt $proc_def_num ]; then  #如果没有进程了
    for ((i=0; i<$diff; i++))
    do
      echo $time_m "boot $taskname" >> $LOG_FILE
      $PHP_BIN $DEAMON_BIN $taskname >> $LOG_FILE 2>&1 &
    done;
  fi
}

#目前是3台服务器(0703)
start_worker()
{
  if [ $APP_ENV == 'test' ]; then
    moni_worker "studyV1" 1 #每台服务器上开1个进程 12个分区
    moni_worker "studyV2" 1 #每台服务器上开1个进程 12个分区
    moni_worker "studyV10" 1 #每台服务器上开1个进程 6个分区
    moni_worker "classbaSchoolPro" 1 #每台服务器上开1个进程 12个分区
    moni_worker "homework" 1 #每台服务器上开1个进程
    moni_worker "activeV1" 1 #每台服务器上开1个进程 12个分区
    moni_worker "activeV2" 1 #每台服务器上开1个进程 12个分区
    moni_worker "activeV10" 1 #每台服务器上开1个进程 6个分区
    moni_worker "summerGuide" 1 #每台服务器上开1个进程
    moni_worker "crystalManage" 1 #每台服务器上开1个进程
    moni_worker "noticeV1" 1 #每台服务器上开1个进程 12个分区
    moni_worker "noticeV2" 1 #每台服务器上开1个进程 12个分区
    moni_worker "noticeV10" 1 #每台服务器上开1个进程 6个分区
    moni_worker "studyReviewStream" 1 #每台服务器上开1个进程
    moni_worker "changeQues" 1 #每台服务器上开1个进程
    moni_worker "knowledge" 1 #每台服务器上开1个进程
    moni_worker "studentTeacherRe" 1 #每台服务器上开1个进程
    moni_worker "courseAllot" 1 #每台服务器上开1个进程
    moni_worker "accountCenter" 1 #每台服务器上开1个进程
    moni_worker "teacherStudentRelation" 1 #每台服务器上开1个进程
    moni_worker "schoolCourse" 1 #每台服务器上开1个进程
    moni_worker "studyOptimalPath" 1 #每台服务器上开1个进程
    moni_worker "supervise" 1 #每台服务器上开1个进程
    moni_worker "schoolChannel" 1 #每台服务器上开1个进程
    moni_worker "studyLmsBdataV2" 1 #每台服务器上开1个进程
    moni_worker "answerRight" 1 #每台服务器上开1个进程
    moni_worker "systemLog" 1 #每台服务器上开1个进程
    moni_worker "deviceLog" 1 #每台服务器上开1个进程
    moni_worker "algoTopic" 1 #每台服务器上开1个进程
    moni_worker "serviceCenterActive" 1 #每台服务器上开1个进程
    moni_worker "latelyQuesRatioSnapshot" 1 #每台服务器上开1个进程
    moni_worker "operationsV2" 1 #每台服务器上开1个进程 12个分区
    moni_worker "operationsV10" 1 #每台服务器上开1个进程 6个分区
    moni_worker "userDlReport" 1 #每台服务器上开1个进程
    moni_worker "studyV2Pre" 4 #每台服务器上开4个进程 12个分区
  elif [ $APP_ENV == 'pre' ]; then
    moni_worker "studyV2Pre" 4 #每台服务器上开4个进程 12个分区
    moni_worker "courseAllot" 1 #每台服务器上开1个进程
  else
    moni_worker "studyV1" 4 #每台服务器上开4个进程 12个分区
    moni_worker "studyV2" 4 #每台服务器上开4个进程 12个分区
    moni_worker "studyV10" 2 #每台服务器上开2个进程 6个分区
  #  moni_worker "studyHistoryV1" 1 #每台服务器上开1个进程
  #  moni_worker "studyHistoryV2" 1 #每台服务器上开1个进程
  #  moni_worker "classbaSchool" 1 #每台服务器上开1个进程
    moni_worker "classbaSchoolPro" 3 #每台服务器上开3个进程 12个分区
    moni_worker "homework" 1 #每台服务器上开1个进程 6个分区
    moni_worker "activeV1" 4 #每台服务器上开4个进程 12个分区
    moni_worker "activeV2" 4 #每台服务器上开4个进程 12个分区
    moni_worker "activeV10" 2 #每台服务器上开2个进程 6个分区
  #  moni_worker "courseSync" 1 #每台服务器上开1个进程 1个分区
    moni_worker "summerGuide" 1 #每台服务器上开1个进程 6个分区
    moni_worker "crystalManage" 1 #每台服务器上开1个进程 6个分区
    moni_worker "noticeV1" 4 #每台服务器上开4个进程 12个分区
    moni_worker "noticeV2" 4 #每台服务器上开4个进程 12个分区
    moni_worker "noticeV10" 2 #每台服务器上开2个进程 6个分区
    moni_worker "studyReviewStream" 1 #每台服务器上开1个进程 6个分区
    moni_worker "changeQues" 2 #每台服务器上开2个进程 6个分区
    moni_worker "knowledge" 1 #每台服务器上开1个进程 6个分区
    moni_worker "studentTeacherRe" 1 #每台服务器上开1个进程 6个分区
    moni_worker "courseAllot" 1 #每台服务器上开1个进程 6个分区
    moni_worker "accountCenter" 1 #每台服务器上开1个进程 3个分区
    moni_worker "teacherStudentRelation" 2 #每台服务器上开2个进程 6个分区
    moni_worker "schoolCourse" 1 #每台服务器上开1个进程 6个分区
    moni_worker "studyOptimalPath" 1 #每台服务器上开1个进程 6个分区
    moni_worker "supervise" 4 #每台服务器上开4个进程 18个分区
    moni_worker "schoolChannel" 1 #每台服务器上开1个进程 6个分区
    moni_worker "studyLmsBdataV2" 1 #每台服务器上开1个进程 6个分区
    moni_worker "answerRight" 2 #每台服务器上开2个进程 6个分区
    moni_worker "systemLog" 2 #每台服务器上开2个进程 6个分区
    moni_worker "deviceLog" 2 #每台服务器上开2个进程 9个分区
    moni_worker "algoTopic" 2 #每台服务器上开2个进程 9个分区
    moni_worker "serviceCenterActive" 2 #每台服务器上开2个进程 9个分区
    moni_worker "latelyQuesRatioSnapshot" 2 #每台服务器上开2个进程 9个分区
    moni_worker "operationsV2" 4 #每台服务器上开4个进程 12个分区
    moni_worker "operationsV10" 2 #每台服务器上开2个进程 6个分区
    moni_worker "userDlReport" 1 #每台服务器上开1个进程
  fi
}

stop_worker()
{
  kill `ps -ef|grep "$DEAMON_BIN"|grep -v "grep"|awk '{print $2}'`
  echo `date` >> "$RUNNING_PATH/killlog"
}

case "$1" in
  "start")
    start_worker
    ps -ef|grep "$DEAMON_BIN"|grep -v "grep"
  ;;
  "stop")
    stop_worker
  ;;
  "check")
    starttime=`date '+%s'`
    while ((`date '+%s'` - starttime < 59));
    do
        start_worker
        sleep 1
    done;
  ;;
  "restart")
    stop_worker
    for ((i=0; i<10; i++))
    do
      sleep 1
    start_worker
    done;
    ps -ef|grep "$DEAMON_BIN"|grep -v "grep"
  ;;
  *)
    echo "Usage: $0 {start|stop|restart}"
    exit 1
  ;;
esac
```