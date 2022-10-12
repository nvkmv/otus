# Add domain from nginx #

in block "trusted_domains" row "array" add:

1 => 'ip adress server',

2 => 'you-domains.com',
   
   
add to /nc_app/config/config.php before bloc "trusted_domains":

'overwritehost' => 'your domain'

## Creating database dump: ##

$ docker exec some-mysql sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /some/path/on/your/host/all-databases.sql

## Restoring data from dump files: ##

$ docker exec -i some-mysql sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < /some/path/on/your/host/all-databases.sql
