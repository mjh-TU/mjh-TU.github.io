### Docker Project

  ## Installed docker on ubuntu machine by first
    Source: https://docs.docker.com/engine/install/ubuntu/
    Set up dockers apt repository
    Installed the docker packages
      sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    Verify
      sudo docker run hello-world

  ## Verified docker compose install using
    docker compose version

  ## Install OpenVAS
    Installed yml file from https://github.com/mikesplain/openvas-docker using command:
      git clone https://github.com/mikesplain/openvas-docker.git

  ## Build and start docker
    ran the command in the directory of the downloaded file
      sudo docker-compose up -d

  ## Issue and next step taken
    Ran into issue, apparently the mikesplain build image is no longer available so going to use https://greenbone.github.io/docs/latest/

    Installed the yml file using this command
      curl -f -L https://greenbone.github.io/docs/latest/_static/docker-compose-22.4.yml -o docker-compose.yml

    Ran into memory issue with greenbone so i just decided to do wordpress, created a directory called wordpress and made new docker-compose.yml and pasted it from their website: https://www.hostinger.com/tutorials/run-docker-wordpress

    Accessed wordpress from browser on port 8000
    Created user and password
    Admin page:
  ![image](https://github.com/mjh-TU/mjh-TU.github.io/assets/124700981/1d99fed7-1a58-48bb-a3ef-bd01d396d476)

    Docker process running:
  ![image](https://github.com/mjh-TU/mjh-TU.github.io/assets/124700981/85b1c892-77da-4e7c-b1fb-1b3677ba543a)

    Docker yml file used for wordpress:
  ![image](https://github.com/mjh-TU/mjh-TU.github.io/assets/124700981/68e3f214-9b56-42c7-91bc-a52117fb3dce)



    
    
      
  
