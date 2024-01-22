```
git stash
git pull -r
git stash pop
fab reboot
```

# Celery Commands

Checks for active running tasks, if
```
celery -A esthenos.tasks inspect active


optional: sudo pkill -9 -f "celery"
```