# 8.0.33がないので、一つ前のマイナーバージョンにする
FROM mysql/mysql-server:8.0.32


ENV MYSQL_DATABASE=laravel \
    MYSQL_USER=phper \
    MYSQL_PASSWORD=secret \
    MYSQL_ROOT_PASSWORD=secret \
    TZ=Asia/Tokyo

COPY ./docker/mysql/conf.d/my.cnf /etc/my.cnf
RUN chmod 644 /etc/my.cnf