# Server Wordpress with PhpMyAdmin


<p align="center">
<img alt="Docker" src="https://banck.net/wp-content/uploads/2015/08/Wordpress_Docker.png" height="300">
</p>
This project is docker compose installation of a single site Wordpress instance for development using Mysql and PhpMyAdmin.

## For existing Wordpress import

### Import Files

Use, for exemple Filezilla, to import files of your Wordpress site. Copy theses files in the directory __./wordpress__.

In __wp-config.php__, you need to change the DB host like this :
```php
/** MySQL hostname */
define('DB_HOST', 'mysql:3306');
```

### Import Database

Make a dump of your database and copy there in the directory __./database__.

For access to the Wordpress instance in local, we need to disable SSL. In the database, search and modify __https to http__ in __siteurl__ and __home__ :
```sql
INSERT INTO `wp_options` (`option_id`, `option_name`, `option_value`, `autoload`) VALUES
(1, 'siteurl', 'http://yourwebsite.com', 'yes'),
(2, 'home', 'http://yourwebsite.com', 'yes'),
```

## Setup Docker-compose file

### Mysql

Replace __${nom_database}__, __${nom_user}__, __${db_password}__ for your values.
```yml
environment:
            MYSQL_DATABASE: ${nom_database}
            MYSQL_USER: ${nom_user}
            MYSQL_PASSWORD: ${db_password}
            MYSQL_RANDOM_ROOT_PASSWORD: 1
            MYSQL_ROOT_PASSWORD: password
```

For import your database, replace __data.sql__ by the name of your sql file:
```yml
volumes:
    - ./database/data.sql:/docker-entrypoint-initdb.d/data.sql
```

### PhpMyAdmin

Replace __${nom_user}__ and __${db_password}__ for your values.
```yml
environment: 
    - PMA_USER=${nom_user}
    - PMA_PASSWORD=${db_password}
```

### Wordpress

You can choose the version of your Wordpress instance with __${wp_version}__. By default choose __latest__.
```yml
image: wordpress:${wp_version}
```

Replace __${nom_user}__, __${db_password}__ and __${nom_database}__ for the correct values like Mysql instance below.
```yml
WORDPRESS_DB_USER: ${nom_user}
WORDPRESS_DB_PASSWORD: ${db_password}
WORDPRESS_DB_NAME: ${nom_database}
```

You can to choose to inject all your wordpress files or only your theme (plugins or not, upload or not) uncomment theses lines to your convenence :
```yml
volumes:
    # - ./wordpress/wp-content/themes:/var/www/html/wp-content/themes
    # - ./wordpress/wp-content/plugins:/var/www/html/wp-content/plugins
    # - ./wordpress/wp-content/uploads:/var/www/html/wp-content/uploads
    # - ./wordpress/wp-config.php:/var/www/html/wp-config.php
    - ./wordpress/:/var/www/html/
```
### /etc/hosts

For access to your Wordpress instance, for this exemple file, to __http://yourwebsite.com__, you need to map your hosts file to redirect to 127.0.0.1. This Docker Wordpress instance run on __http://localhost__ by default.

Add to your file :
```
127.0.0.1       localhost
127.0.1.1       yourwebsite.com
```

### Build the project

For build your Wordpress the first time, use the command :
```sh
docker-compose up --build
```

And access to [http://yourwebsite.com](http://yourwebsite.com)

Next time you can run your project with the command:
```sh
docker-compose up
```
