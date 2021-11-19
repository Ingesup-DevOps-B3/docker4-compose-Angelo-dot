



# Exercice 1

```yaml
version: "3.3"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - /home/user/Documents/Ynov/DevOps/Docker/wordpress/db:/var/lib/mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - /home/user/Documents/Ynov/DevOps/Docker/wordpress:/var/www/html
    ports:
      - "8000:80"
    restart: unless-stopped
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}

```

Je rajoute l'argument ``unless-stopped`` à restart pour indiquer le redemarrage seulement en cas de crash.





Lancer le docker: 

```
docker-compose up
```

Le résultat lors de l'ouverture de la page localhost:8000

![](wordpress.png)

# Exercice 2



```yaml
version: "3"

services:

traefik:
    image: traefik:v2.3
    command: --api.insecure=true --providers.docker
    ports:
      - 85:80
      - 8580:8080
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    networks:
      - logging-network

  filebeat:
    image: docker.elastic.co/beats/filebeat:7.5.0
    user: root
    depends_on:
      - elasticsearch
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - logging-network
    environment:
      - -strict.perms=false

  kibana:
    image: docker.elastic.co/kibana/kibana:7.5.0
    depends_on:
      - elasticsearch
    ports:
      - 5601:5601
    networks:
      - logging-network

networks:
  logging-network:
    driver: bridge


```



D'après un post de Stack Overflow, ``docker.sock`` est le socket Unix que le deamon Docker écoute. C'est l'entrypoint de l'API Docker. Il peut également s'agir d'un socket TCP, mais par défaut pour des raisons de sécurité, Docker utilise par défaut le socket UNIX. 

https://stackoverflow.com/questions/35110146/can-anyone-explain-docker-sock

