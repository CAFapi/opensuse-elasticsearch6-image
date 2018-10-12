#
# Copyright 2017-2018 Micro Focus or one of its affiliates.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# Reference the official Elasticsearch 6 image
FROM docker.elastic.co/elasticsearch/elasticsearch:6.3.2 AS es6-image

#
# The actual image definition
#
FROM cafapi/opensuse-jre8:1

ENV ELASTIC_CONTAINER=true

COPY --from=es6-image /opt/jdk-10.0.2/ /opt/jdk-10.0.2/
ENV JAVA_HOME=/opt/jdk-10.0.2

# Copy java certs and set link to them
COPY --from=es6-image /etc/pki/ca-trust /etc/pki/ca-trust
RUN ln -sf /etc/pki/ca-trust/extracted/java/cacerts /opt/jdk-10.0.2/lib/security/cacerts

# Install packages
RUN zypper --non-interactive -n install which hostname netcat-openbsd unzip wget
RUN zypper --non-interactive clean --all

RUN mkdir -p /usr/share/elasticsearch
# Create group, user and set access rights to the elasticsearch base dir
RUN groupadd -g 1000 elasticsearch && \
    useradd -u 1000 -g 1000 -G 0 -d /usr/share/elasticsearch elasticsearch && \
    chmod 0775 /usr/share/elasticsearch && \
    chgrp 0 /usr/share/elasticsearch

WORKDIR /usr/share/elasticsearch

COPY --from=es6-image --chown=1000:0 /usr/share/elasticsearch/ /usr/share/elasticsearch/
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin install analysis-icu

# Configure logging
COPY logging.yml /usr/share/elasticsearch/config

ENV PATH=/usr/share/elasticsearch/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

COPY --from=es6-image --chown=1000:0 /usr/local/bin/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chgrp 0 /usr/local/bin/docker-entrypoint.sh && \
    chmod g=u /etc/passwd && \
    chmod 0775 /usr/local/bin/docker-entrypoint.sh

COPY scripts/ /opt/elasticsearch/scripts/
RUN chmod +x /opt/elasticsearch/scripts/*

EXPOSE 9200 9300
CMD /opt/elasticsearch/scripts/start.sh

# Tag the image
ARG BUILD_NUMBER
ARG BUILD_DATE
ARG GIT_REPO
ARG GIT_BRANCH
ARG GIT_COMMIT

LABEL Build.Number="$BUILD_NUMBER" \
      Build.Date="$BUILD_DATE" \
      Git.Repo="$GIT_REPO" \
      Git.Branch="$GIT_BRANCH" \
      Git.Commit="$GIT_COMMIT"
