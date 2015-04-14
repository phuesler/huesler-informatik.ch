---
layout: post
title: "Mysql trouble"
---

````
mysql -u root foo_db < ~/Downloads/dump.sql
ERROR 2006 (HY000) at line 268: MySQL server has gone away
find /usr/local -name *.cnf
/usr/local/Cellar/mariadb/10.0.17/bin/my_print_defaults
cp -f /usr/local/Cellar/mariadb/10.0.17/support-files/my-large.cnf /usr/local/etc/my.cnf
sed '268q;d' ~/Downloads/dump.sql > /tmp/file.sql && ls -lah /tmp/file.sql && rm /tmp/file.sql
sed '266q;d' ~/Downloads/dump.sql > /tmp/file.sql
max_allowed_packet = 3M

$: find /usr/local -name "my*.cnf"
/usr/local/Cellar/mariadb/10.0.17/support-files/my-huge.cnf
/usr/local/Cellar/mariadb/10.0.17/support-files/my-innodb-heavy-4G.cnf
/usr/local/Cellar/mariadb/10.0.17/support-files/my-large.cnf
/usr/local/Cellar/mariadb/10.0.17/support-files/my-medium.cnf
/usr/local/Cellar/mariadb/10.0.17/support-files/my-small.cnf
/usr/local/etc/my.cnf
/usr/local/etc/my.cnf.d/mysql-clients.cnf
````

