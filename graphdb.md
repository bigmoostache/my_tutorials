Docker image for GraphDB

```bash
sudo docker pull ontotext/graphdb:9.11.2-ee
```

Create volume for GraphDB
```bash
sudo docker volume create graphdb-data
```

Run GraphDB
```bash
sudo docker run -p 127.0.0.1:7200:7200 --name graphdb-instance-name -v graphdb-data:/opt/graphdb/home -t ontotext/graphdb:9.11.2-ee -d
```
```

Access GraphDB
```bash
docker exec -it graphdb-instance-name bash
```

Access logs
```bash
docker logs graphdb-instance-name
```