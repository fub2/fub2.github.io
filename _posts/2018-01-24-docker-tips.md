## commands
* ```docker rm $(docker stop $(docker ps -a -q --filter ancestor=<image-name> --format="{{.ID}}"))```

## Break into a running container

* attach

```
$ sudo docker attach 665b4a1e17b6 #by ID
or
$ sudo docker attach loving_heisenberg #by Name
$ root@665b4a1e17b6:/# 
```

* exec

```
$ sudo docker exec -i -t 665b4a1e17b6 /bin/bash #by ID
or
$ sudo docker exec -i -t loving_heisenberg /bin/bash #by Name
$ root@665b4a1e17b6:/#
```
