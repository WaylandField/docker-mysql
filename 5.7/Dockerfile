FROM debian:wheezy

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r mysql && useradd -r -g mysql mysql

RUN apt-get update && apt-get install -y \
		bison \
		build-essential \
		cmake \
		curl \
		libncurses5-dev

ENV MYSQL_MAJOR 5.7
ENV MYSQL_VERSION 5.7.4-m14

RUN mkdir /usr/src/mysql \
	&& curl -SL "https://dev.mysql.com/get/Downloads/MySQL-$MYSQL_MAJOR/mysql-$MYSQL_VERSION.tar.gz" \
		| tar -xzC /usr/src/mysql --strip-components=1 \
	&& cd /usr/src/mysql \
	&& cmake . -DCMAKE_BUILD_TYPE=Release \
		-DWITH_EMBEDDED_SERVER=OFF \
	&& make -j"$(nproc)" \
	&& make test \
	&& make install \
	&& cd .. \
	&& rm -rf mysql \
	&& rm -rf /usr/local/mysql/mysql-test \
	&& rm -rf /usr/local/mysql/sql-bench \
	&& find /usr/local/mysql -type f -name "*.a" -delete \
	&& { find /usr/local/mysql -type f -executable -exec strip --strip-all '{}' + || true; }
ENV PATH $PATH:/usr/local/mysql/bin:/usr/local/mysql/scripts

WORKDIR /usr/local/mysql
VOLUME /var/lib/mysql

ADD docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 3306
CMD ["mysqld", "--datadir=/var/lib/mysql", "--user=mysql", "--lower_case_table_names=1"]
