### 1. Deploy MongoDB using Docker:

1. First, make sure you have Docker installed on your machine.

2. Pull the MongoDB Docker image:

   ```bash
   sudo docker pull mongo:latest
   ```

3. Create a volume for MongoDB data:

   ```bash
      sudo docker volume create mongodb_data
   ```

4. Run MongoDB using Docker:

   ```bash
      sudo docker run --name mongo_container -v mongodb_data:/data/db -p 27017:27017 -d mongo:latest
   ```

   This will start a MongoDB instance with a named container `mongo_container`, bind its data directory to the volume `mongodb_data` you created, and expose it on the default MongoDB port `27017`.

### 2. Import JSON Data to MongoDB:

1. Save your JSON data (like the one you showed) into a file named `data.json`.

2. Copy the `data.json` file to the running MongoDB container:

   ```bash
      sudo docker cp data.json mongo_container:/data.json
   ```

3. Use `mongoimport` to import the JSON file to the MongoDB database:

   ```bash
      sudo docker exec -it mongo_container mongoimport --db metadataMulti --collection crossref --file /data.json
   ```

   Replace `yourDatabaseName` and `yourCollectionName` with appropriate names for your database and collection, respectively.

### 3. Index DOIs, URLs, and Titles:

1. Enter the MongoDB container shell:

   ```bash
      sudo docker exec -it mongo_container mongo
   ```

2. Switch to your database:

   ```javascript
   use metadataMulti
   ```

3. Create indexes on the DOI, URL, and title fields. Assuming these are the fields in your data:

   ```javascript
   db.yourCollectionName.createIndex({ "prefix": 1 });
   db.yourCollectionName.createIndex({ "URL": 1 });
   // For title, you didn't provide the field in the given JSON data. Assuming "title" as the field name.
   db.yourCollectionName.createIndex({ "title": 1 });
   db.yourCollectionName.createIndex({ "URL": 1 }, { unique: true })
   ```

   The number `1` denotes an ascending index.

4. After you're done, you can exit the MongoDB shell by typing `exit`.

That's it! You've now deployed a MongoDB database using Docker, imported your JSON data into it, and created indexes on the DOIs, URLs, and titles.

## Various MongoDB Operations

### 1. Preventing Duplicate Entities:

MongoDB provides a unique index feature that can help prevent duplicate entries based on specific fields. 

For example, if you consider the `URL` field unique and don't want any duplicates for it:

1. Enter the MongoDB container shell:
   ```bash
   docker exec -it mongo_container mongo
   ```

2. Switch to your database:
   ```javascript
   use yourDatabaseName
   ```

3. Create a unique index for the `URL` field:
   ```javascript
   db.yourCollectionName.createIndex({ "URL": 1 }, { unique: true })
   ```

Now, when you try to import an entity with the same URL again using `mongoimport`, it will be rejected.

However, note that when using `mongoimport` with unique constraints, if there's a conflict, the entire import operation will be halted. This can be problematic if you're trying to insert many documents at once, and only a few have conflicts. You might need to preprocess your data or handle conflicts programmatically for more sophisticated use cases.

### 2. MongoDB UI:

There are many GUIs available for MongoDB. One of the most popular ones is MongoDB Compass.

To use MongoDB Compass with your Dockerized MongoDB:

1. Download and install [MongoDB Compass](https://www.mongodb.com/try/download/compass).

2. Open MongoDB Compass.

3. Connect to your MongoDB database. Usually, the connection string will be something like:
   ```
   mongodb://localhost:27017/yourDatabaseName
   ```

Another popular GUI tool is Mongo Express, which can be run as a Docker container. Here's a basic way to deploy it alongside your MongoDB container:

1. Run Mongo Express connected to your MongoDB instance:
   ```bash
   docker run -it --link mongo_container:mongo -p 8081:8081 mongo-express
   ```

2. Open a web browser and navigate to `http://localhost:8081` to access the Mongo Express web interface.

Using these GUI tools, you can visually explore your MongoDB collections, documents, and even perform CRUD operations without having to use the command line.


## Docker compose for mongodb + mongo-express

### Quick Explanation on Docker Compose:

Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you can define a multi-container application in a single file, then use a single command to spin up all of those containers and make them work together.

The core component of Docker Compose is the `docker-compose.yml` file. This file describes the services, networks, and volumes you want to use. Each service is essentially a Docker container.

The most basic commands with Docker Compose are:

- `docker-compose up`: This command reads the `docker-compose.yml` file and starts all the defined services.
  
- `docker-compose down`: This shuts down and removes all the service containers defined.


### Writing and Deploying MongoDB + Mongo Express using Docker Compose:

1. **Define the `docker-compose.yml` file**

    Here's a basic example of what your `docker-compose.yml` might look like for MongoDB and Mongo Express:

    ```yaml
    version: '3'

    services:
      mongo:
        image: mongo:latest
        volumes:
          - mongodb_data:/data/db
        ports:
          - "27017:27017"
          
      mongo-express:
        image: mongo-express
        ports:
          - "8081:8081"
        environment:
          ME_CONFIG_MONGODB_SERVER: mongo
        depends_on:
          - mongo

    volumes:
      mongodb_data:
    ```

    Note:

    - The `mongo` service is our MongoDB server.
    - The `mongo-express` service is the Mongo Express UI.
    - The `depends_on` ensures that the `mongo-express` service only starts after the `mongo` service has started.
    - We have declared a volume `mongodb_data` that is attached to the `mongo` service.

2. **Start the services**

   Navigate to the directory containing your `docker-compose.yml` and run:

   ```bash
   docker-compose up
   ```

   This will start both MongoDB and Mongo Express. Mongo Express will be able to communicate with MongoDB because they are defined in the same Compose file and are, therefore, on the same network.

3. **Accessing the services**

   - MongoDB will be accessible on port 27017 of your host.
   - Mongo Express will be accessible on port 8081 of your host.

4. **Shutting down the services**

   When you're done, you can shut down the services by running:

   ```bash
   docker-compose down
   ```

   If you want to also remove the defined volumes (like our `mongodb_data`), you can run:

   ```bash
   docker-compose down -v
   ```

And that's how you can deploy MongoDB and Mongo Express simultaneously using Docker Compose!