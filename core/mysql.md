# MySQL Support

## Overview

API Platform comes with PostgreSQL as default database, it is possible to use MySQL instead by following these steps.

## Enabling MySQL Support
If you are using the [API Platform Distribution](../distribution/index.md), modify the `Dockerfile` to add the extension:

```diff
# api/Dockerfile
	\
	docker-php-ext-configure zip; \
	docker-php-ext-install -j$(nproc) \
		intl \
		pdo_pgsql \
+   pdo_mysql \
		zip \
	; \
```

Then rebuild the `php` image:

    $ docker-compose build php
    
Add a MySQL 8 Server image to the docker-compose file:

```yaml
# docker-compose.yml
# ...
  db-mysql:
      # In production, you may want to use a managed database service
      image: mysql/mysql-server
      environment:
          - MYSQL_DATABASE=api
          - MYSQL_USER=api-platform
          # You should definitely change the password in production
          - MYSQL_PASSWORD=!ChangeMe!
          - MYSQL_ROOT_PASSWORD=!ChangeMe!
          # If you have problems with older clients, lower security and go oldschool
          #- DEFAULT_AUTHENTICATION_PLUGIN=mysql_native_password
      volumes:
          - db-data:/var/lib/mysql:rw
          # You may use a bind-mounted host directory instead, so that it is harder to accidentally remove the volume and lose all your data!
          # - ./docker/db/data:/var/lib/mysql:rw
      ports:
          - "3306:3306"
# ...
```

Change the MongoDB environment variables to match your Docker image:

```
# api/.env
DATABASE_URL=mysql://api-platform:!ChangeMe!@db-mysql/api?server_version=8
```

Change the doctrine configuration of API Platform to add mysql support:

# api/config/doctrine.yaml
```yaml
dbal:
    # ...
    driver: 'pdo_mysql'
    server_version: '8'
    #driver: 'postgresql'
    #server_version: '12'
    # ...
 ```

 
