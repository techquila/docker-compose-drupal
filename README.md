# Drupal Docker development made easy

* See https://docs.docker.com/compose/
* More details on docker compose file: https://docs.docker.com/compose/compose-file

Focus on easy set-up and light images with Alpine Linux.

## Features
* Easy to launch, include all base tools for Drupal
* Code and data persistence as mounted in data/www and data/database
* All services logs in data/logs
* Default config override from config folder
* Base Php / Apache / Nginx images with bash and custom PS1 (docker exec -it CONTAINER_NAME bash)
* Light images (based on Alpine Linux)
* One service by containers

### Include (every service is optionnal as declared in the yml file)
* Php 5.6 or 7 with Xdebug
* Apache and/or Nginx
* MySQL and/or PostgreSQL
* Memcache
* Mailhog
* Solr
* Ldap
* Varnish

### Include Drupal/Php Tools:
* Drush
* Drupal console
* Composer
* Adminer

## Quick launch new Drupal project

<pre>
# Clone this project.
git clone https://github.com/Mogtofu33/docker-compose-drupal.git docker-drupal
cd docker-drupal

# Create your compose file from template.
cp docker-compose.tpl docker-compose.yml

# Edit docker-compose.yml depending services you want.
vi docker-compose.yml

# Launch the containers.
docker-compose build && docker-compose up -d

# Check your containers statuses and names.
docker-compose ps
</pre>

Download and install Drupal 7 with Apache and MySQL, replace WEB_CONTAINER_NAME with the one running from previous command.

Change drupal-7 to drupal for last 8.x release.
<pre>
docker exec -u apache:www-data WEB_CONTAINER_NAME drush dl drupal-7 -y --destination=/www --drupal-project-rename
docker exec -u apache:www-data WEB_CONTAINER_NAME drush --root=/www/drupal si -y --db-url=mysql://drupal:drupal@mysql/drupal --account-name=admin --account-pass=password
</pre>

#### Go to your Drupal, login with admin/password:

* [http://localhost/drupal](http://localhost/drupal)

#### MySQL / PostgreSQL :
* Database host (from apache or nginx container):
 * mysql
 * pgsql
* database name / user / pass: drupal

#### Solr core (from apache or nginx container):
* [http://solr:8983/solr/drupal](http://solr:8983/solr/drupal)

## See containers logs
<pre>docker-compose logs</pre>

See data/logs for specific services logs.

## Destroy containers
<pre>docker-compose stop && docker-compose down</pre>

## Remove your data (and lost everything !)
<pre>sudo rm -rf data/database data/logs data/www/drupal</pre>

## Containers access

### See running services and get container names
<pre>docker-compose ps</pre>

### Execute command on any service
<pre>docker exec -it CONTAINER_NAME MY_CMD</pre>

### Bash access on services based on my images
<pre>docker exec -it CONTAINER_NAME bash</pre>

### Other images (not from my base)
<pre>docker exec -it CONTAINER_NAME /bin/sh</pre>

## Recommended tools

- PimpMyLog:
<pre>git clone https://github.com/potsky/PimpMyLog.git data/www/TOOLS/PimpMyLog</pre>

 - Copy config from config/pimpmylog

- phpMemcachedAdmin
<pre>git clone https://github.com/wp-cloud/phpmemcacheadmin.git data/www/TOOLS/PhpMemcachedAdmin</pre>

  - (Change config 127.0.0.1 to memcache)

- opcache gui
<pre>git clone https://github.com/amnuts/opcache-gui.git data/www/TOOLS/Opcache-gui</pre>

- Xdebug gui
<pre>git clone https://github.com/splitbrain/xdebug-trace-tree.git data/www/TOOLS/Xdebug-trace</pre>

## Services access from host

* Adminer and other tools access:
 * [http://localhost/TOOLS](http://localhost/TOOLS)
* Mailhog access:
 * [http://localhost:8025](http://localhost:8025)
* Solr access:
 * [http://localhost:8983](http://localhost:8983)
* Ldap admin:
 * login: cn=admin,dc=example,dc=org
 * pass: admin
 * [http://localhost:6443](http://localhost:6443)
* More ldap info, see https://github.com/osixia/docker-openldap#environment-variables

## Using Drush within your container

Using docker exec you can run a command directly in the container, for example:
 docker exec -it CONTAINER_NAME drush --root=/www/drupal status

You can find a script to set a Drush alias for your container, you must supply user, group and container name on first run:
<pre>. scripts/drush-start.sh apache:www-data CONTAINER_NAME</pre>
Every drush command will now run on this container.

When you finish your work on this stack:
<pre>. scripts/drush-end.sh</pre>

## More features on next release

* SSL on Apache / Nginx
* Data permissions fix setting host user uid/gid to service owner.
