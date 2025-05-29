
## Initial Stepps
first create volumes
```
$ echo nginx_certs nginx_logs f2b | xargs -n 1 docker volume create
```

then create a network
```
$ docker network create --subnet=172.16.111.0/24 certbot-network
```

## Certbot /Let's encrypt

### Run Certbot in standalone setup without reverse proxy

```
docker run -it --rm \
-p 80:80 \
-v "nginx_certs:/etc/letsencrypt" \
--name cb \
"certbot/certbot" \
certonly --verbose --keep-until-expiring --agree-tos --email <your-email> --preferred-challenges http --standalone -d "<domain1>" -d "<domain2>"
```

### Run Certbot behind reverse proxy

```
docker run -it --rm \
--network certbot-network \
--ip 172.16.111.10 \
-v "nginx_certs:/etc/letsencrypt" \
--name cb \
"certbot/certbot" \
certonly --verbose --keep-until-expiring --agree-tos --email <your-email> --preferred-challenges http --standalone -d "<domain1>" -d "<domain2>"
```


## Utility / Cheatsheet
```
docker exec -it f2b fail2ban-client set <jail> unbanip <ip-address>
docker exec nginx nginx -s reload

docker run -it --rm -v nginx_html:/nginx-html -v ./html:/new-html/ busybox cp -a /new-html/. /nginx-html/
docker run -it --rm -v f2b:/f2b  busybox cat /f2b/banlist.txt

docker run -it --rm -v nginx_logs:/logs  busybox ls -lh /logs/
docker run -it --rm -v nginx_logs:/logs  busybox truncate -s 0 /logs/*.log
```
