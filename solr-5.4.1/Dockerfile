FROM java:openjdk-8-jre

RUN apt-get update && \
  apt-get -y install lsof && \
  rm -rf /var/lib/apt/lists/*

ENV SOLR_USER solr
ENV SOLR_UID 8983

RUN groupadd -r -g $SOLR_UID $SOLR_USER && \
  useradd -r -u $SOLR_UID -g $SOLR_USER $SOLR_USER

ENV SOLR_KEY E6E21FFCDCEA14C95910EA65051A0FAF76BC6507
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$SOLR_KEY"

ENV SOLR_VERSION 5.4.1
ENV SOLR_SHA256 3e4b4ec7bd728b49b2ebc3dbe8f3d1ef89fded4ab86b9e2f856bedd58c99f28b

RUN mkdir -p /opt/solr && \
  wget -nv http://archive.apache.org/dist/lucene/solr/$SOLR_VERSION/solr-$SOLR_VERSION.tgz -O /opt/solr.tgz && \
  wget -nv http://archive.apache.org/dist/lucene/solr/$SOLR_VERSION/solr-$SOLR_VERSION.tgz.asc -O /opt/solr.tgz.asc && \
  gpg --verify /opt/solr.tgz.asc && \
  echo "$SOLR_SHA256 */opt/solr.tgz" | sha256sum -c - && \
  tar -C /opt/solr --extract --file /opt/solr.tgz --strip-components=1 && \
  rm /opt/solr.tgz* && \
  mkdir -p /opt/solr/server/solr/lib && \
  sed -i -e 's/#SOLR_PORT=8983/SOLR_PORT=8983/' /opt/solr/bin/solr.in.sh && \
  sed -i -e '/-Dsolr.clustering.enabled=true/ a SOLR_OPTS="$SOLR_OPTS -Dsun.net.inetaddr.ttl=60 -Dsun.net.inetaddr.negative.ttl=60"' /opt/solr/bin/solr.in.sh && \
  chown -R $SOLR_USER:$SOLR_USER /opt/solr

COPY ./config /temp/solr-drupal-config

RUN mkdir -p /opt/solr/server/solr/gpii-dspace/conf && \
    mkdir -p /opt/solr/server/solr/gpii-dspace/data && \
    cd /temp/solr-drupal-config && cp -f * /opt/solr/server/solr/gpii-dspace/conf/

COPY ./core.properties /temp/core.properties
RUN cp -f /temp/core.properties /opt/solr/server/solr/gpii-dspace/core.properties
RUN chown -R solr:solr /opt/solr/server/solr

EXPOSE 8983
WORKDIR /opt/solr
USER $SOLR_USER
CMD ["/opt/solr/bin/solr", "-f"]