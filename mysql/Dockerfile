FROM mysql:8.0.16
 
RUN mkdir -p /mysql
COPY db-script.sql /mysql
COPY mysql_init.sh /docker-entrypoint-initdb.d
 
RUN chmod a+x /docker-entrypoint-initdb.d/mysql_init.sh
