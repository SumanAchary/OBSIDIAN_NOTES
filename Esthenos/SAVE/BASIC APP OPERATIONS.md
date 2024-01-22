<hr>

### IMPORT
```
from esthenos.tasks import *
```

### FETCH APP
```
app = EsthenosOrgApplication.objects.get(application_id = "AR231228000001")
```
### UPDATE APP
```
app.update_status(245)
```

### FETCH USER
```
user = EsthenosUser.objects.get(email = 'killershark@save-dev.esthenos.com')
```
