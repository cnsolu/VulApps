FROM medicean/vulapps:base_lamp

MAINTAINER cnsolu <cnsolu@gmail.com>

COPY src/CmsEasy_5.6_UTF-8_20160825.tar.gz /tmp/CmsEasy_5.6_UTF-8_20160825.tar.gz
COPY src/cmseay.sql /tmp/cmseay.sql


RUN set -x \
    && apt-get install -y php5-mysql php5-dev php5-gd php5-memcache php5-pspell php5-snmp snmp php5-xmlrpc libapache2-mod-php5 php5-cli unzip \
    && ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/ \
    && rm -rf /var/www/html/* \
    && tar -zxf /tmp/CmsEasy_5.6_UTF-8_20160825.tar.gz -C /var/www/html --strip-components=1 \
    && rm -rf /tmp/CmsEasy_5.6_UTF-8_20160825.tar.gz



RUN set -x \
    && chown -R www-data:www-data /var/www/html/ \
    && /etc/init.d/mysql start \
    && mysql -e "CREATE DATABASE cmseasy DEFAULT CHARACTER SET utf8;" -uroot -proot \
    && mysql -e "use cmseasy;source /tmp/cmseay.sql;" -uroot -proot

EXPOSE 80
CMD ["/start.sh"]
