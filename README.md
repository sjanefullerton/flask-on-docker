# Flask on Docker (Creating a Simple Web App)
Included in this repo is information to dockerize Flask with Postgres, Gunicorn, and Nginx. A comprehensive tutorial can be found [here](https://testdriven.io/blog/dockerizing-flask-with-postgres-gunicorn-and-nginx/).     

## Overview
Using a modified Instagram tech stack, dockerizing Flask allows us to get a fully working web service. This was done by creating necessary files for a simple web app. To do this, Flask was configured to run on Docker with Postgres. Nginx and Gunicorn were added for production environments and serving static and user-uploaded media files (via Nginx). The web service follows a microservices architecture, with Docker containers encapsulating different components. Requests flow through Nginx, which acts as a reverse proxy, forwarding requests to Gunicorn for processing Flask application logic. This setup enables users to test a production environment locally and ensures effective handling of static and media files. We can see an example of locally uploading and viewing a user-uploaded media file here:

![Upload GIF](upload.gif)

## Build Instructions
*Dependencies:*
1. Flask v2.3.2
2. Docker v23.0.5
3. Python v3.11.3

*Please Note: Docker and Docker Compose must be installed. Instructions to set this up can be found [here](https://docs.docker.com/engine/security/rootless/#install). This project utilized rootless docker*.

### Development

*(Uses Postgres)*

1. Build the images and spin up containers:

    ```sh
    $ docker-compose up -d --build
    ```
2. Navigate to test it at [http://localhost:7447](http://localhost:7447). You should see:
   ```sh
   {
     "hello": "world"
   }
   ```
3. Spin down development containers (and associated volumes with `-v` flag.)
    ```sh
    $ docker compose down -v
    ```
### Production

*(Uses gunicorn & nginx)
(*make sure to spin down development containers before proceeding*)

1. Build the production images and spin up containers:
    ```sh
    $ docker-compose -f docker-compose.prod.yml up -d --build
    ```
2. Navigate to test it at [http://localhost:7447](http://localhost:7447). You should see:
     ```sh
     {
       "hello": "world"
     }
     ```
3. Spin down development containers (and associated volumes with `-v` flag.)
    ```sh
    $ docker-compose -f docker-compose.prod.yml down -v
    ```
### Apply the model to the database
(*make sure to spin down development containers before proceeding*)
1. Build new image and spin up containers:
    ```sh
    $ docker-compose up -d --build
    ```
2. Create the table:
    ```sh
    $ docker-compose exec web python manage.py create_db
    ```
3. Ensure table creation:
    ```sh
    $ docker-compose exec db psql --username=hello_flask --dbname=hello_flask_dev
    ```
    
    Keys Information:
    
    `# \l`: List of databases
    
    `# \c hello_flask_dev`: connects to database "hello_flask_dev" as user "hello_flask"

    `# \dt`: List of relations

    `# q`: exit/quit
4.  Navigate to test at [http://localhost:7447](http://localhost:7447)
5. Bring down development containers
    ```sh
    $ docker-compose down -v
    ```
### Handling Static Files
#### Development
1. Re-build images and spin up containers: `docker-compose up -d --build`
2. Ensure [http://localhost:7447/static/hello.txt](http://localhost:5001/static/hello.txt) serves up the file correctly
3. Spin down development containers: `docker-compose down -v`
#### Production
1. Re-build images and spin up containers: `docker-compose -f docker-compose.prod.yml up -d --build`
2. Navigate to ensure static asset loaded correctly: [http://localhost:7447/static/hello.txt](http://localhost:5001/static/hello.txt)
3. Spin down containers: `docker-compose -f docker-compose.prod.yml down -v`

### Handling Media Files
#### Development
1. Re-build images and spin up containers: `docker-compose up -d --build`
2. Navigate to [http://localhost:7447/upload](http://localhost:7447/upload).
* Upload an image
* View the image at [http://localhost:7447/media/IMAGE_FILE_NAME](http://localhost:7447/media/IMAGE_FILE_NAME)
3. Spin down development containers: `docker-compose down -v`
#### Production
1. Re-build:
    ```sh
    $ docker-compose -f docker-compose.prod.yml up -d --build
    $ docker-compose -f docker-compose.prod.yml exec web python manage.py create_db
    ```
2. Test:
* Upload an image at [http://localhost:7447/upload](http://localhost:7447/upload)
* View image at [http://localhost:7447/media/IMAGE_FILE_NAME](http://localhost:7447/media/IMAGE_FILE_NAME)


