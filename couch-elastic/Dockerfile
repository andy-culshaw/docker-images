FROM ubuntu:14.04
MAINTAINER Andy Culshaw <andy.culshaw@gmail.com>

# Install utilities
RUN apt-get -qq update
RUN apt-get -y install curl apt-utils librtmp0 python-pip

# Environment Variables
ENV CB_VERSION		2.2.0
ENV CB_RELEASE_URL	http://packages.couchbase.com/releases
ENV CB_PACKAGE		couchbase-server-community_${CB_VERSION}_x86_64.deb
ENV CB_USERNAME		Administrator
ENV CB_PASSWORD		couchbase
ENV ES_PKG_NAME		elasticsearch-0.90.4.deb

# Install Couchbase
RUN curl -LO $CB_RELEASE_URL/$CB_VERSION/$CB_PACKAGE
RUN dpkg -i $CB_PACKAGE
RUN rm $CB_PACKAGE
RUN ln -s /opt/couchbase/bin/couchbase-cli /usr/local/bin/
RUN mkdir -p /cb-data/ /cb-index
RUN chown couchbase:couchbase /cb-data /cb-index
VOLUME /cb-data
VOLUME /cb-index

# Install Elasticsearch
RUN apt-get -y install openjdk-7-jre
RUN curl -LO https://download.elasticsearch.org/elasticsearch/elasticsearch/$ES_PKG_NAME
RUN dpkg -i $ES_PKG_NAME
RUN rm $ES_PKG_NAME
RUN mkdir /es-data
RUN chown elasticsearch:elasticsearch /es-data
VOLUME /es-data

# Elasticsearch Head Plugin
RUN /usr/share/elasticsearch/bin/plugin -install mobz/elasticsearch-head
RUN mv /usr/share/elasticsearch/plugins/head /usr/share/elasticsearch/plugins/_site
RUN mkdir /usr/share/elasticsearch/plugins/head
RUN mv /usr/share/elasticsearch/plugins/_site /usr/share/elasticsearch/plugins/head
# Elasticsearch Couchbase Transport Plugin
RUN /usr/share/elasticsearch/bin/plugin -install transport-couchbase -url http://packages.couchbase.com.s3.amazonaws.com/releases/elastic-search-adapter/1.2.0/elasticsearch-transport-couchbase-1.2.0.zip
ADD elasticsearch.yml /etc/elasticsearch/elasticsearch.yml

# Put start script
ADD couchbase-start /usr/local/bin/
ADD elasticsearch-start /usr/local/bin/
ADD init-script /usr/local/bin/
RUN chmod +x /usr/local/bin/couchbase-start /usr/local/bin/elasticsearch-start /usr/local/bin/init-script


# Expose Couchbase ports
EXPOSE 4369 8091 8092 21100:21299
# Expose Elasticsearch ports
EXPOSE 9091 9200 9300

CMD ["init-script"]
