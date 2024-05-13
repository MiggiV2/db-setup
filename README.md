# Postgresql Setup with Docker
## Prerequisites:
- Ensure Docker is installed on your system. If it's not, you can download it from the official Docker website.
- Basic familiarity with terminal or command prompt use.

## Option 1 docker-compose (with pgAdmin)

### Setup 1:
Clone this repository.
```shell
git clone https://github.com/MiggiV2/db-setup.git
```

### Step 2:
Go into the direcotry.
```shell
cd db-setup
```

### Setup 3:
Start the service with docker-compose. (Add the -d flag to run detach)
```shell
docker compose up
```

### Setup 4:
Access pgAdmin via your browser and login (see below).  
Go to http://localhost:8080.  
E-Mail: `student@thi.de`  
Password: `123456789#`.

### Setup 5:
Connect to your postgres container.  
Host: `postgres`  
User: `student`  
Password: `123456789#`

### Explanation for Step 5:
Docker compose create a virtual Docker network.  
Both containers are in this network and can reach each other.  
`postgres` is the service name from docker-compose.yaml. If you want to read more, see [How to reach docker containers by name instead of IP address?](https://stackoverflow.com/questions/31149501/how-to-reach-docker-containers-by-name-instead-of-ip-address)

As postgres host, localhost:5432 will not work! The pgAdmin would use localhost inside the container, instead the docker host (maschine) localhost.

## Option 2 docker run

### Step 1: Create a Docker Network
First, we'll create a Docker network. This network will allow the containers to communicate with each other. In this case, it will let the container running `psql` connect to the PostgreSQL database container.

Open your terminal or command prompt and run:
```shell
docker network create db
```

### Step 2: Run the PostgreSQL Container
Next, we'll use Docker to run a container based on the `postgres:11` image. This is not the latest version, but it was recommended by the teacher. Version 16 would be up-to-date. We'll name this container `my-postgres`, set a password (in this case, "password") for the PostgreSQL default user (`postgres`), link it to the network `db` we created, and run it in detached mode.

Run the following command:
```shell
docker run --name my-postgres -e POSTGRES_PASSWORD=password --network db -d postgres:11
```

### Step 3: Connect to Your PostgreSQL Database
Now that your PostgreSQL server is up and running in a Docker container, you can connect to it using `psql`, the command-line interface for interacting with PostgreSQL. We'll run this in another container and remove it once we're done, hence the `--rm` flag.

To connect, use:
```shell
docker run -it --rm --network db postgres:11 psql -h my-postgres -U postgres
```
This command does a few things:
- `docker run -it` tells Docker to run this container in interactive mode so that you can interact with `psql`.
- `--rm` means Docker will automatically remove the container when it's stopped. This is handy for one-off commands to avoid cluttering up your system with stopped containers.
- `--network db` connects this container to the `db` network, so it can find `my-postgres`.
- `postgres:11` specifies the Docker image to use, which matches the version of the database you are running.
- `psql -h my-postgres -U postgres` connects `psql` to your PostgreSQL server. The `-h` flag specifies the host, which is the name of your PostgreSQL container. The `-U` flag specifies the user to connect as.

### Step 4: Exiting `psql`
To exit `psql`, simply type `\q` and press enter.

### Additional Tips
- Always ensure your password is secure and change "password" to a strong, secure one, especially if you're using this setup for anything beyond local testing.
- To manage data persistence, consider using Docker volumes to store your PostgreSQL data. This ensures your data survives container restarts and deletions.

That's it! You've successfully set up PostgreSQL in Docker and can begin creating databases, tables, and running SQL queries. Happy coding!

# Next start
### Step 1: Start postgres
After a system reboot, the container will be stopped. You need to start the container again.
```shell
docker start my-postgres
```

### Step 2: Connect
Same command from Step 3. The container was removed because of the --rm option.
```shell
docker run -it --rm --network db postgres:11 psql -h my-postgres -U postgres
```