<hr>
[[ONE GO RESTART]]

```
sudo pkill -9 -f "esthenos-celery"
sudo pkill -9 -f "esthenos-celery2"
sudo pkill -9 -f "esthenos-celery3"
sudo pkill -9 -f "esthenos-celery4"
sudo pkill -9 -f "esthenos-celery5"
sudo pkill -9 -f "esthenos-celery6"
sudo pkill -9 -f "esthenos-beats"

sudo monit restart esthenos-celery
sudo monit restart esthenos-celery2
sudo monit restart esthenos-celery3
sudo monit restart esthenos-celery4
sudo monit restart esthenos-celery5
sudo monit restart esthenos-celery6

sudo monit restart esthenos-webapp
sudo monit restart esthenos-beats 

sudo monit summary
```
# CHECK LOGS

`sudo tail -f /var/log/esthenos/esthenos-celery.log`

# KILL CELERY
```
 
 /bin/kill `/bin/cat /var/run/esthenos/esthenos-celery.pid` 
 /bin/kill `/bin/cat /var/run/esthenos/esthenos-celery2.pid`  
 /bin/kill `/bin/cat /var/run/esthenos/esthenos-celery3.pid`  
 /bin/kill `/bin/cat /var/run/esthenos/esthenos-celery4.pid`  
 /bin/kill `/bin/cat /var/run/esthenos/esthenos-celery5.pid`  
 /bin/kill `/bin/cat /var/run/esthenos/esthenos-celery6.pid`

```
# START CELERY
```
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery2
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery3
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery4
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery5
/bin/bash /home/ubuntu/esthenos/e_deploy/esthenos-start.sh esthenos-celery6
```

# RABBIT MQ. PERMISSIONS / RESTART

```
sudo rabbitmqctl add_user esthenos-tasks esthenos  
sudo rabbitmqctl add_vhost /esthenos-tasks  
sudo rabbitmqctl set_permissions -p /esthenos-tasks esthenos-tasks ".*" ".*" ".*"
sudo service rabbitmq-server restart
```


# Celery Logs

```
tail -f /var/log/esthenos/esthenos-celery.log
```

# Web App Logs

```
tail -f /var/log/esthenos/esthenos-webapp.log
```