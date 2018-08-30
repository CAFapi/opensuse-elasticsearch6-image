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

#
# Preliminary image that downloads and extracts Elasticsearch
#
FROM cafapi/opensuse-jre8:1 AS builder

RUN zypper -n install which hostname
RUN zypper -n install unzip

# Download and Extract Elasticsearch
ADD https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.2.zip /opt/

RUN unzip /opt/elasticsearch-6.3.2.zip -d /opt/

# Install the analysis-icu plugin for Elasticsearch
RUN /opt/elasticsearch-6.3.2/bin/elasticsearch-plugin install analysis-icu

# Configure logging
COPY logging.yml /opt/elasticsearch-6.3.2/config

#
# The actual image definition
#
FROM cafapi/opensuse-jre8:1

RUN zypper -n install which hostname

# Startup Elasticsearch and expose ports
EXPOSE 9200 9300
CMD /opt/elasticsearch/scripts/start.sh

COPY --from=builder /opt/elasticsearch-6.3.2/ /opt/elasticsearch/

COPY scripts/ /opt/elasticsearch/scripts/
RUN chmod +x /opt/elasticsearch/scripts/*

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
