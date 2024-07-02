# 1、登录(action=login)，获取session.id

```
curl --location -k  'https://192.168.5.132:8081' \
--request post \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'action=login' \
--data-urlencode 'username=hackwu' \
--data-urlencode 'password=123'
###
response -->
{
  "session.id" : "1f080ca2-ba04-4d64-965e-1e9b5797f066",
  "status" : "success"
}
```

# 2、创建项目(action=create) 

```
curl --location -k   'https://192.168.5.132:8443/manager' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=9a7d24aa-269d-48d3-b478-aa839b717836' \
--data-urlencode 'action=create' \
--data-urlencode 'name=apiTest2023' \
--data-urlencode 'description=desc'
###
response -->
{"path":"manager?project=apiTest2023","action":"redirect","status":"success"}
```

# 3、删除项目

```
curl --location -k  -G 'https://192.168.5.132:8443/manager' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=9a7d24aa-269d-48d3-b478-aa839b717836' \
--data-urlencode 'delete=true' \
--data-urlencode 'project=apiTest2023'
#会返回HTML页面，但是项目是删除成功的。
```

# 4、查询Flow 信息

```
curl --location -k 'https://192.168.5.132:8443/executor' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=8aa55086-fe96-4c7c-9da5-120e0445a19c' \
--data-urlencode 'ajax=fetchexecflow' \
--data-urlencode 'execid=6'
```

# 5、查询Flow 的执行记录

```
curl --location -k -G 'https://192.168.5.132:8443/manager' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=9a7d24aa-269d-48d3-b478-aa839b717836' \
--data-urlencode 'ajax=fetchFlowExecutions' \
--data-urlencode 'project=first' \
--data-urlencode 'flow=first' \
--data-urlencode 'start=0' \
--data-urlencode 'length=1000'
curl --location -k -G 'https://192.168.5.132:8443/executor' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=9a7d24aa-269d-48d3-b478-aa839b717836' \
--data-urlencode 'ajax=fetchexecflows' \
--data-urlencode 'project=jobs' 
--data-urlencode 'start=0' \
--data-urlencode 'length=10'
curl -b "azkaban.browser.session.id=<session_id>"
 http://<azkaban_host>:<port>/executor?ajax=fetchExecFlow&project=<project_name>
```

# 6、执行Flow

```
curl --location -k -X POST  'https://192.168.5.132:8443/executor' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=8aa55086-fe96-4c7c-9da5-120e0445a19c' \
--data-urlencode 'ajax=executeFlow' \
--data-urlencode 'project=first' \
--data-urlencode 'flow=first' 
##
response -->
{
  "project" : "first",
  "message" : "Execution submitted successfully with exec id 7",
  "flow" : "first",
  "execid" : 7
}
```

# 7、取消Flow(测试未通过)

```
curl --location -k    'https://192.168.5.132:8443/executor' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=8aa55086-fe96-4c7c-9da5-120e0445a19c' \
--data-urlencode 'ajax=cancelFlow' \
--data-urlencode 'project=first' \
--data-urlencode 'flow=first' 
```

# 8、查看执行的Job日志

```
curl --location -k    'https://192.168.5.132:8443/executor' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=8aa55086-fe96-4c7c-9da5-120e0445a19c' \
--data-urlencode 'ajax=fetchExecJobLogs' \
--data-urlencode 'execid=7' \
--data-urlencode 'jobId=first' \
--data-urlencode 'offset=0' \
--data-urlencode 'length=1000' 
```

# 9、获取到所有的项目(测试未通过)

```
curl --location -k    'https://192.168.5.132:8443/index' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--cookie 'azkaban.browser.session.id=9a7d24aa-269d-48d3-b478-aa839b717836' \
--data-urlencode 'ajax=fetchuserprojects'
curl --location -k 'https://192.168.5.132:8443/index?ajax=fetchuserprojects&session.id=8aa55086-fe96-4c7c-9da5-120e0445a19c'
```