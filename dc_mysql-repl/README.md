# dc_mysql-repl #

change in slave.sql ip adress user and password

## Restore dump from file  ##
docker exec -i repl_db mysql -uroot -pdb123 -e "create database ncdb"

docker exec -i repl_db mysql -uroot -pdb123 ncdb < ncdb.sql

