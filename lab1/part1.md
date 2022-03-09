- facem pull la imaginea de busybox:
```bash
docker image pull busybox
```
- verificam ca exista imaginea de busybox local:
```bash
docker images | grep "busybox"
```
- executam comanda `uptime` intr-un container de busybox:
```bash
docker container run busybox uptime
```
- pornim interactiv un container de busybox, unde putem da comenzi fix in terminalul din container:
```bash
docker container run -it  busybox
```
- pornim interactiv, in background, un container de busybox, in care putem sa intram folosind `docker attach <id-container>`:
```bash
docker container run -d -it  busybox
```
- vedem ce containere de busybox avem, ca sa le stergem (folosind `docker container rm <id-container>):
```bash
docker container ls -a | grep "busybox"
```