# NEW COMMANDS

```
sudo systemctl restart celery_bank  
sudo systemctl restart celery_default  
sudo systemctl restart celery_high  
sudo systemctl restart celery_kyc  
sudo systemctl restart celery_report  
sudo systemctl restart celery_fileupload
```


# ONE GO RESTART

```

/bin/kill `/bin/cat /var/run/esthenos/esthenos-celery.pid` 
/bin/kill `/bin/cat /var/run/esthenos/esthenos-celery2.pid`  
/bin/kill `/bin/cat /var/run/esthenos/esthenos-celery3.pid`  
/bin/kill `/bin/cat /var/run/esthenos/esthenos-celery4.pid`  
/bin/kill `/bin/cat /var/run/esthenos/esthenos-celery5.pid`  
/bin/kill `/bin/cat /var/run/esthenos/esthenos-celery6.pid`

/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery2
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery3
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery4
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery5
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery6

sudo rabbitmqctl set_permissions -p /esthenos-tasks esthenos-tasks ".*" ".*" ".*"
sudo service rabbitmq-server restart

sudo monit restart esthenos-webapp
sudo monit restart esthenos-beats 

sudo monit restart all 
sudo monit summary

```



