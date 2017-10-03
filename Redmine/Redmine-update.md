
# Redmine upgrade from 2.6.3 to 3.4 (latest stable) && move it from edw01 machine to artemis machine

## Staging steps  

1. on edw01 machine  dump the database (we will need it to migrate the dump on artemis)

`# mysqldump -u redmine -p redmine_new > redmine_edw.sql`


2. Migrate `redmine_edw.sql` and `website files` from `edw01` to `artemis`
> (the following steps are executed on local machine)
```
sudo su
scp andra@edw01:/home/andra/redmine_new.sql .
scp redmine_new.sql  edwsys@artemis.edw.ro:/home/edwsys
scp   andra@edw01.edw.ro:/home/andra/files.tar  .
scp files.tar  edwsys@artemis.edw.ro:/home/edwsys

vim /etc/hosts // add 88.99.2.80 sitename.ro…
```



3. Import the dump from mysql server on edw01 on redmine_mysql container
> on artemis machine
```
docker exec -it redmine_mysql bash
mysql -u redmine -p redmine < /var/local/redmine/backup/redmine_new.sql
```


4. Letsencript cert for artemis (on artemis machine)
```
yum install certbot-apache
certbot --apache certonly -d  artemis.eaudeweb.ro
certbot --apache -d informea-support.edw.ro
certbot --update-registration register
```

5. Add https
> or artemis machine
```
vim  /etc/httpd/conf.d/40-helpdesk.conf 
service httpd status 
service httpd restart
service httpd status 
```
6. on artemis 
`docker-compose up -d`

7. Test it in browser 



