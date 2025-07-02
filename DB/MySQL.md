``` toc
```
``` mysql
# create database
SHOW DATABASES;
CREATE DATABASE test_db;
USE test_db;

# create table
SHOW TABLES;
CREATE TABLE `alert_groups` (
  `group_name` varchar(255) NOT NULL,
  `business_dept` varchar(255) NOT NULL DEFAULT 'N.A',
  PRIMARY KEY (`group_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
DESCRIBE alert_groups;

# insert data
LOAD DATA LOCAL INFILE 'xx.txt' INTO TABLE alert_groups;
INSERT INTO alert_groups VALUES ('global_group', 'IIB');

```