# Laravel-Docker

This image provides an plug-and-play environment for your Laravel application to run.
The image can be used for production in a Dockerfile, but also can be ran with docker run or docker-compose for 
your development environments.

## Maintainers

This docker image is maintained by two enthusiastic dutch companies.
- [Dutch & Bold](https://www.dutchandbold.com) - Rotterdam, the Netherlands
- [Webbits](https://www.webbits.nl) - Rijswijk, the Netherlands

## Docker

Using docker run is a nice basic method to get your files up and running quickly.

```bash
docker run \
    --name=example \
    -v ./:/web \
    -p 80:80 \
    dutchandbold/laravel-docker
```

### Dockerfile

For production this is the preferred method. Use this image in your docker file and COPY your project's files into 
the container.

For example:

```dockerfile
FROM dutchandbold/laravel-docker:latest

ENV WORKER_NUMPROCS 1

COPY . /web
RUN composer install -d /web
```

### Docker-compose

For development this might be the best method to run your project. You can use this image and mount your files into it.

For example:
```yaml
version: '3.3'
services:
  app:
    image: dutchandbold/laravel-docker:latest
    ports:
      - '80:80'
    restart: always
    volumes:
      - ./:/web
    environment:
      DB_HOST: db
    links:
      - db
    depends_on:
      - db

  db:
    image: mysql
    restart: always
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    environment:
      MYSQL_DATABASE: 'homestead'
      MYSQL_USER: 'homestead'
      MYSQL_PASSWORD: 'secret'
      MYSQL_ROOT_PASSWORD: 'secret'
volumes:
  mysql-data:
```

## Laravel

This package is made to work for Laravel out of the box. It supports features such as a supervised queue or
the scheduler out of the box.

### Queue Worker

By default the queue worker is not enabled. But not to worry. We have made it really easy to enable.
To enable the queue worker you only have to set an environment variable.

|Environment variable|Default value|Description                |
|--------------------|-------------|---------------------------|
|`WORKER_NUMPROCS`   |0            |Number of workers          |
|`WORKER_SLEEP`      |3            |Sleep between jobs         |
|`WORKER_TRIES`      |3            |Max attempts if a job fails|
|`WORKER_TIMEOUT`    |60           |Timeout for frozen jobs    |

More info at [Laravel docs | Queues](https://laravel.com/docs/5.6/queues)

### Scheduler

The laravel scheduler runs by default if artisan exists in the `/web` folder

## PHP

This image is based on the official PHP image PHP7.2-fpm.

### Configuration

One option to configure php is to overwrite the php.ini like so:

```
COPY php.ini /usr/local/etc/php/
```

But we have also made some options available through environment variables.

|Environment variable     |Default value|Description                |
|-------------------------|-------------|---------------------------|
|`PHP_MEMORY_LIMIT`       |128M         |memory_limit               |
|`PHP_POST_MAX_SIZE`      |8M           |post_max_size              |
|`PHP_UPLOAD_MAX_FILESIZE`|2M           |upload_max_filesize        |