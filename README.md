
# Example Voting App

A simple distributed application running across multiple Docker containers.

## Architecture
Through this architecture we can understand the concept of this application.

![architecture excalidraw](https://github.com/anshuvarshney/POCs/assets/115215127/08bb579f-82af-4dc0-8b7a-2785c1201d76)
 - A front-end web app in Python which lets you vote between two options
- A Redis which collects new votes
- A .NET worker which consumes votes and stores them in…
- A Postgres database backed by a Docker volume
- A Node.js web app which shows the results of the voting in real time
## Getting started

Before starting, you will need to check the Docker is running on our machine or not.  
    
     
          sudo systemctl status docker
    

## Step-1:
A front-end web app in Python which lets you vote between two options
 
- first create a directory for this project and next, change the directory to your project:

    ```bash
        mkdir docker_project
        cd docker_project
    ```
- Download the code 
    ```bash
        git clone https://github.com/dockersamples/example-voting-app.git
    ```
- Change the directory to example-voting-app
    ```bash
        cd example-voting-app
    ```
- Through ls command check the vote directory is present or not. If present then change the directory
    ```bash
        cd vote 
    ```
- In the vote directory have Dockerfile 
- Now build that application through the Dockerfile
    ```bash
        docker build  -t voting-app . 
    ```

- Check the docker images
    ```bash
         docker images  
    ```     
    voting-app image is in list.
- Run the voting app on the localhost
    ```bash
           docker run -d -p 5000:80  voting-app
    ```
- Now, open your web browser and access your voting-app container using the URL http://0.0.0.0:5000 . You should see the voting-app page on the following screen:

![Screenshot from 2023-10-26 14-10-40](https://github.com/anshuvarshney/POCs/assets/115215127/a6d126f6-588f-4b9c-9e72-ee232840df32)


- We can't vote at that point.
If we give the vote cats vs dogs it shows an error like Internal Server Error!! 

## Step-2:
A Redis which collects new votes

```bash
    docker run -d --name=redis redis  
```
Above this command it create an immage of redis and check the images through the docker images command.

- Voting-app votes are linked with the redis through this command:
 
    ```bash
    docker run -d -p 5000:80 --link redis:redis voting-app
    ```

- Now, open your web browser and access your voting-app and refresh it...

Now you will see a page where you can vote between two options (Cats vs Dogs) without any internal server issue.

![Screenshot from 2023-10-27 12-31-33](https://github.com/anshuvarshney/POCs/assets/115215127/0a004daf-4fb6-497b-b359-80431cee9825)

It works fine now..

## Step-3:

A Postgres database backed by a Docker volume.
- Create a container for postgres db and it's version  

   ```bash
    docker run --name=db -e POSTGRES_PASSWORD=postgres -d postgres:9.4
  
    ```
## Step-4:
A .NET worker which consumes votes and stores them in…
- Now create a .NET worker-app container for votes and follow these command 
    ```bash
      cd ..
      cd worker/
    ```
    Now check the Dockerfile and built it.

- For build 
    ```bash
        docker build -t worker-app .
    ```
Now, we can easily link the redis and database server postgres.
```bash
    docker run -d --link=redis:redis --link=db:db worker-app
```
Hence we connect properly till now.

## Step-5:
A Node.js web app which shows the results of the voting in real time.

This is the final step of our project...

- First follow these commands
    ```bash
      cd ..
      cd result/
    ```
- Create a container for result of voting app
    ```bash
       docker build -t result-app .
    ```
- Now check the docker images
    ```bash
       docker images
    ```
In output we have a multiple docker images.

- Run the Result app on the localhost
    ```bash
       docker run -d -p 5001:80 --link db:db result
    ```

- Now, open your web browser and access your voting-app container using the URL http://0.0.0.0:5000 and also access your result-app container using the URL http://0.0.0.0:5001  . You should see the voting-app page and result page in different tab :

- so, it can find database for by db name . because all container are communicate by name of container in same natwork.

### Check url and refresh it.....

![Screenshot from 2024-02-08 12-23-09](https://github.com/anshuvarshney/voting_app/assets/115215127/e0aff95f-80f6-443d-a718-2bdac13024ce)
![Screenshot from 2024-02-08 12-23-16](https://github.com/anshuvarshney/voting_app/assets/115215127/5afab38b-bc00-4fca-bdea-cb2c6ea63ac4)
![Scree![Screenshot from 2024-02-08 13-06-03](https://github.com/anshuvarshney/voting_app/assets/115215127/b7833337-9909-495c-a2a5-a75213f1d3bd)

![Screenshot from 2024-02-08 12-23-01](https://github.com/anshuvarshney/voting_app/assets/115215127/bb69391f-c7e2-4044-b299-abc93f84f578)


## Notes 
The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to deal with them in Docker at a basic level.
