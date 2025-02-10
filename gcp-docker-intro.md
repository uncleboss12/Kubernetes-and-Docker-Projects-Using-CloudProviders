
# Using Google clod to build and push a docker image to the google artifact Repository
gcloud auth list

gcloud config list project

docker run hello-world

docker images

docker run hello-world

docker ps

docker ps -a

mkdir test && cd test

cat > Dockerfile <<EOF
### Use an official Node runtime as the parent image
FROM node:lts

### Set the working directory in the container to /app
WORKDIR /app

### Copy the current directory contents into the container at /app
ADD . /app

### Make the container's port 80 available to the outside world
EXPOSE 80

### Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF


cat > app.js << EOF;
const http = require("http");

const hostname = "0.0.0.0";
const port = 80;

const server = http.createServer((req, res) => {
	res.statusCode = 200;
	res.setHeader("Content-Type", "text/plain");
	res.end("Hello World\n");
});

server.listen(port, hostname, () => {
	console.log("Server running at http://%s:%s/", hostname, port);
});

process.on("SIGINT", function () {
	console.log("Caught interrupt signal and will exit");
	process.exit();
});
EOF


docker build -t node-app:0.1 .

docker images


docker run -p 4000:80 --name my-app node-app:0.1

curl http://localhost:4000

docker stop my-app && docker rm my-app

docker run -p 4000:80 --name my-app -d node-app:0.1

docker ps

docker logs d1383bd651d2

cd test

docker build -t node-app:0.2 .

docker run -p 8080:80 --name my-app-2 -d node-app:0.2
docker ps

curl http://localhost:8080


docker logs -f d1383bd651d2

docker exec -it [container_id] bash

docker exec -it d1383bd651d2 bash

docker inspect [container_id]

docker inspect d1383bd651d2

docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]

docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' d1383bd651d2

# push to google Artifact Repo 
gcloud auth configure-docker us-west1-docker.pkg.dev

### or 

gcloud artifacts repositories create my-repository --repository-format=docker --location=us-west1 --description="Docker repository"


# push to the Repo 

docker build -t us-west1-docker.pkg.dev/qwiklabs-gcp-04-cce5c91acf7e/my-repository/node-app:0.2 .

docker images

docker push us-west1-docker.pkg.dev/qwiklabs-gcp-04-cce5c91acf7e/my-repository/node-app:0.2

# Test the Image
docker stop $(docker ps -q)
docker rm $(docker ps -aq)

docker rmi us-west1-docker.pkg.dev/qwiklabs-gcp-04-cce5c91acf7e/my-repository/node-app:0.2
docker rmi node:lts
docker rmi -f $(docker images -aq) # remove remaining images
docker images

docker run -p 4000:80 -d us-west1-docker.pkg.dev/qwiklabs-gcp-04-cce5c91acf7e/my-repository/node-app:0.2

curl http://localhost:4000



