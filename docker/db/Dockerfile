FROM mysql:5.7.25
LABEL "Project"="Vprofile"
LABEL "Author"="Nikhil"

ENV MYSQL_ROOT_PASSWORD="admin123"
ENV MYSQL_DATABASE="accounts"

ADD accountsdb.sql /docker-entrypoint-initdb.d/accountsdb.sql