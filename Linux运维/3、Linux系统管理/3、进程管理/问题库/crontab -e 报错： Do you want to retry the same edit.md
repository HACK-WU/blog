crontab -e 报错： Do you want to retry the same edit

[root@bogon ~]# crontab -e
 

no crontab for root - using an empty one


crontab: installing new crontab
 "/tmp/crontab.rbsEaa":2: bad hour
 

errors in crontab file, can't install.
Do you want to retry the same edit? y


crontab: installing new crontab
"/tmp/crontab.rbsEaa":2: bad hour


errors in crontab file, can't install.
 Do you want to retry the same edit? 





原因：可能是格式错误

前面的* * * * * 相互之间必须有空格  ！














