MariaDB-Galera-Cluster-bitnami
https://github.com/bitnami/bitnami-do...
my_mariabackup_user = lekAdmin
my_mariabackup_password = 123123
my_root_password = ROOTlek152
my_user = myUser
my_password = 123123

docker pull bitnami/mariadb-galera:latest

# Step 1: Bootstrap the cluster

```
    docker run --name mariadb-galera-0 \
    -p 23300:3306 \
    -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node0:/bitnami/mariadb \
    -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node0/my_custom.cnf:/opt/bitnami/mariadb/conf/my_custom.cnf:ro \
    -e NODE_NAME=NODE0 \
    -e MARIADB_GALERA_CLUSTER_BOOTSTRAP=yes \
    -e MARIADB_GALERA_FORCE_SAFETOBOOTSTRAP=yes \
    -e MARIADB_GALERA_CLUSTER_NAME=my_galera \
    -e MARIADB_GALERA_MARIABACKUP_USER=lekAdmin \
    -e MARIADB_GALERA_MARIABACKUP_PASSWORD=123123 \
    -e MARIADB_ROOT_PASSWORD=ROOTlek152 \
    -e MARIADB_USER=myUser \
    -e MARIADB_PASSWORD=123123 \
    -e MARIADB_DATABASE=my_database \
    -d bitnami/mariadb-galera:latest

```

# Step 2: Add nodes to the cluster (1,2,3,4)

```
    docker run --name mariadb-galera-1 --link mariadb-galera-0:mariadb-galera \
    -p 23301:3306 \
    -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node1:/bitnami/mariadb \
    -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node1/my_custom.cnf:/opt/bitnami/mariadb/conf/my_custom.cnf:ro \
    -e NODE_NAME=NODE1 \
    -e MARIADB_GALERA_CLUSTER_NAME=my_galera \
    -e MARIADB_GALERA_CLUSTER_ADDRESS=gcomm://mariadb-galera \
    -e MARIADB_GALERA_MARIABACKUP_USER=lekAdmin \
    -e MARIADB_GALERA_MARIABACKUP_PASSWORD=123123 \
    -d bitnami/mariadb-galera:latest
```


docker run --name mariadb-galera-2 --link mariadb-galera-0:mariadb-galera \
  -p 23302:3306 \
  -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node2:/bitnami/mariadb \
  -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node2/my_custom.cnf:/opt/bitnami/mariadb/conf/my_custom.cnf:ro \
  -e NODE_NAME=NODE2 \
  -e MARIADB_GALERA_CLUSTER_NAME=my_galera \
  -e MARIADB_GALERA_CLUSTER_ADDRESS=gcomm://mariadb-galera \
  -e MARIADB_GALERA_MARIABACKUP_USER=lekAdmin \
  -e MARIADB_GALERA_MARIABACKUP_PASSWORD=123123 \
  -d bitnami/mariadb-galera:latest  


docker run --name mariadb-galera-3 --link mariadb-galera-0:mariadb-galera \
  -p 23303:3306 \
  -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node3:/bitnami/mariadb \
  -v /Users/chaiyasittayabovorn/Desktop/mariadb_cluster/node3/my_custom.cnf:/opt/bitnami/mariadb/conf/my_custom.cnf:ro \
  -e NODE_NAME=NODE3 \
  -e MARIADB_GALERA_CLUSTER_NAME=my_galera \
  -e MARIADB_GALERA_CLUSTER_ADDRESS=gcomm://mariadb-galera \
  -e MARIADB_GALERA_MARIABACKUP_USER=lekAdmin \
  -e MARIADB_GALERA_MARIABACKUP_PASSWORD=123123 \
  -d bitnami/mariadb-galera:latest  



ip a (check ip-address)

ps -ef | grep mysql
echo $NODE_NAME


docker exec -it mariadb-galera-0 /bin/bash
docker exec -it mariadb-galera-1 /bin/bash
docker exec -it mariadb-galera-2 /bin/bash
docker exec -it mariadb-galera-3 /bin/bash

# shell testing (must wait to finish setting)
mysql -u myUser -p 
123123

# show cluster info
SHOW STATUS LIKE '%cluster%';


# enable remote login permission
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' IDENTIFIED BY 'password';
SELECT user FROM mysql.user GROUP BY user;


# try to create table and insert some data
show databases;
use my_database;
CREATE TABLE example ( id smallint unsigned not null auto_increment, name varchar(20) not null, constraint pk_example primary key (id) );
INSERT INTO example ( id, name ) VALUES ( null, 'Hey CodeMobiles' );
select * from example;


use my_database;
select * from example;

docker stop mariadb-galera-3
docker stop mariadb-galera-2
docker stop mariadb-galera-1
docker stop mariadb-galera-0

docker rm mariadb-galera-3 mariadb-galera-2 mariadb-galera-1 mariadb-galera-0 -f

docker restart mariadb-galera-0
docker restart mariadb-galera-1
docker restart mariadb-galera-2
docker restart mariadb-galera-3# mariadb-cluster
