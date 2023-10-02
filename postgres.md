```bash
sudo docker pull postgres
sudo docker volume create postgres_data
sudo docker run --name postgres_container -e POSTGRES_PASSWORD=b78gako91g28kPOI09 -d -p 5432:5432 -v postgres_data:/var/lib/postgresql/data postgres
```
To destroy the container:
```bash
sudo docker stop postgres_container
sudo docker rm postgres_container
sudo docker volume rm postgres_data
```


To deploy a .sql file to the database (not working)
```bash
sudo docker cp <file.sql> postgres_container:/tmp
sudo docker exec -it postgres_container bash
psql -U postgres -f /tmp/<file.sql>
```

To connect to the sql shell
```bash
sudo docker exec -it postgres_container bash
psql -U postgres
```

To list all databases
```postgresql
\l
```

To create a database and import a .sql file into it
```postgresql
CREATE DATABASE <database_name>;
\c <database_name>
\i <file.sql>
```

To read the first 1000 symbols of a file
```bash
head -c 1000 <file>
```

