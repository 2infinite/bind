# bind

[BIND](https://www.isc.org/downloads/bind/) server running on [Alpine Linux:latest](https://hub.docker.com/_/alpine/).

## Usage:

### Create a data directory on the host system for store configs, zone data, pid file...:
```sh
mkdir -p /var/ct/bind/etc/bind /var/ct/bind/var/bind /var/ct/bind/var/run/named
```

### Copy config files:
tar xvf [bind.tgz](https://github.com/2infinite/bind/raw/master/bind.tgz) -C /var/ct/bind/etc/

### Run BIND server:
```sh
docker run -d --log-driver=syslog --restart=always --name bind --read-only=true \
-v /var/ct/bind/etc/bind:/etc/bind -v /var/ct/bind/var/bind:/var/bind \
-v /var/ct/bind/var/run/named:/var/run/named --cpuset-cpus="0-1" \
--memory="256m" --kernel-memory="128m" --memory-swap="512m" -p 53:53/udp \
-p 53:53/tcp --security-opt no-new-privileges 2infinity/bind:latest
```

### Set permissions:
```sh
docker exec bind chown -R root:named /var/bind /var/run/named
docker exec bind chmod -R 770 /var/bind /var/run/named
```

### Make key for rndc:
```sh
docker exec bind rndc-confgen -a 
docker exec bind chown named /etc/bind/rndc.key
vi named.conf.local and remove comments after //run rndc-confgen...
```

### Restart container:
```sh
docker restart bind
```
