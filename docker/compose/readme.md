# Docker compose template for deploying apps
```
version: '3.8'

services:
  sample-app1-fe:
    image: image.registry.cloud/project-name/image-name:latest
    container_name: sample-app1-fe
    ports:
      - "localhost:8081:80"
    env_file:
      - .env-pda
#    environment:
#      - NODE_ENV=production
#      - APP_PORT=3000
#      - DATABASE_URL=mysql://user:pass@mysql:3306/app1_db
    networks:
      - app1-nw
    depends_on:
      - mysql

  sample-app1-be:
    image: image.registry.cloud/project-name/image-name:latest
    container_name: sample-app1-be
    ports:
      - "localhost:3000:3000"
#    env_file:
#      - .env-pda
    environment:
      - NODE_ENV=production
      - APP_PORT=3000
      - DATABASE_URL=mysql://user:pass@mysql:3306/app1_db
    networks:
      - app1-nw
    depends_on:
      - mysql

  mysql:
    image: mysql:8
    container_name: app1-mysql
    ports:
      - "localhost3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root-pas-value
      MYSQL_DATABASE: app1-db
      MYSQL_PASSWORD: passvalue
    volumes:
      - ./mysql_data:/var/lib/mysql
      - ./app1.sql:/docker-entrypoint-initdb.d/app1.sql
    networks:
      - app1-nw

networks:
  app1-nw:
    external: true
```