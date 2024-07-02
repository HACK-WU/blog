**Zabbix 联合钉钉实现终端报警**

# 1 ）监控方式添加参数

```auto
{ALERT.SENDTO}
{ALERT.SUBJECT}
{ALERT.MESSAGE}
```

# 2 ）错误报警信息参数

## 标题改为：

```auto
故障{TRIGGER.STATUS},服务器:{HOSTNAME1}发生: {TRIGGER.NAME}故障!
```

## 信息改为：

```auto
告警主机:{HOSTNAME1}
告警时间:{EVENT.DATE} {EVENT.TIME}
告警等级:{TRIGGER.SEVERITY}
告警信息: {TRIGGER.NAME}
告警项目:{TRIGGER.KEY1}
问题详情:{ITEM.NAME}:{ITEM.VALUE}
当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}
事件 ID:{EVENT.ID}
```

# 3 ）错误恢复报警信息设置

## 标题改为：

```auto
恢复{TRIGGER.STATUS}, 服务器:{HOSTNAME1}: {TRIGGER.NAME}已恢复!
```

## 信息改为：

```auto
告警主机:{HOSTNAME1}
告警时间:{EVENT.DATE} {EVENT.TIME}
告警等级:{TRIGGER.SEVERITY}
告警信息: {TRIGGER.NAME}
告警项目:{TRIGGER.KEY1}
问题详情:{ITEM.NAME}:{ITEM.VALUE}
当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}
事件 ID:{EVENT.ID}
```

# token.sh

```
#!/bin/bash
to=$1
subject=$2
text=$3
curl 'https://oapi.dingtalk.com/robot/send?access_token=758cefbfde6108910a29fad7e35edd9ff76255df5ac16871ad7cf03b2262a7fc' \
-H 'Content-Type: application/json' \
-d '
{"msgtype": "text",
"text": {
"content": "'"$text"'"
},
"at":{
"atMobiles": [ "'"$1"'" ],
"isAtAll": false
}
}'
```