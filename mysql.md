```bash
sudo docker pull mysql
sudo docker volume create mysqldata
sudo docker  run --name mysql_container -e MYSQL_ROOT_PASSWORD=123456 -d -p 3306:3306 -v mysqldata:/var/lib/mysql mysql
```

**To load a sql file**
```bash
sudo docker cp file.sql mysql_container:/file.sql
sudo docker exec -it mysql_container mysql -uroot -p123456
CREATE DATABASE mydatabase;
USE mydatabase;
source /file.sql;
exit;
```

**To connect a phpmyadmin UIX**
```bash
sudo docker pull phpmyadmin/phpmyadmin
sudo docker run --name my-phpmyadmin -d --link mysql_container:db -p 8080:80 phpmyadmin/phpmyadmin
sudo docker stop my-phpmyadmin
sudo docker start my-phpmyadmin
```

the default user is root and the password is 123456
