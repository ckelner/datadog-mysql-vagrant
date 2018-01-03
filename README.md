# datadog-mysql-vagrant
This was built to test reporting on a remote MySQL instance. Therefore this
Vagrant doesn't install MySQL locally. This was slapped together rather quickly
so it is missing automation for a number of steps.


* Run [MySQL via Docker](https://hub.docker.com/_/mysql/):
  ```
  docker run -p 3306:3306 --name kelnerhax-mysql -e MYSQL_ROOT_PASSWORD=<password> -d mysql:latest
  ```
* `docker inspect <container-name>` to get the container ip from `NetworkSettings.IPAddress"
* `docker exec -it <container-name> /bin/bash` to get on the docker container
* Follow https://docs.datadoghq.com/integrations/mysql/#prepare-mysql
  * `mysql -u root --password=<password>`
  * `CREATE USER 'datadog'@'%' IDENTIFIED BY '<dd-mysql-password>';`
  * `GRANT REPLICATION CLIENT ON *.* TO 'datadog'@'%' WITH MAX_USER_CONNECTIONS 5;`
  * `GRANT PROCESS ON *.* TO 'datadog'@'%';`
  * `exit`
  * `mysql -u datadog --password=<dd-mysql-password> -e "show status" | grep Uptime && echo -e "\033[0;32mMySQL user - OK\033[0m"`
* **For sake of time this vagrant doesn't automatically configure the MySQL YAML simply `vagrant ssh` and configure by hand for now**
  * Use the special virtualbox IP `10.0.2.2` to connect to the mysql instance via the host on port `3306`
* Spin up a second vagrant the same way using `./second_vagrant/Vagrantfile`
* This will result in hosts reporting the same metric (duplicate) as seen here:
  ![two-hosts-one-db](https://i.imgur.com/3WD3M1C.png)
