FROM ruby:2.2-slim

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r redmine && useradd -r -g redmine redmine

RUN apt-get update && apt-get install -y --no-install-recommends \
		ca-certificates \
		wget \
	&& rm -rf /var/lib/apt/lists/*


RUN apt-get update && apt-get install -y --no-install-recommends \
		imagemagick \
		libmysqlclient18 \
		libpq5 \
		libsqlite3-0 \
		\
		bzr \
		git \
		mercurial \
		openssh-client \
		subversion \
	&& rm -rf /var/lib/apt/lists/*

ENV RAILS_ENV production
WORKDIR /usr/src/redmine

ENV REDMINE_VERSION 3.2.4
ENV REDMINE_DOWNLOAD_MD5 fe9c81d9abdf21ddafd03f5d1c1f8a58

#RUN wget -O redmine.tar.gz "https://www.redmine.org/releases/redmine-${REDMINE_VERSION}.tar.gz" \
#	&& echo "$REDMINE_DOWNLOAD_MD5 redmine.tar.gz" | md5sum -c - \
#	&& tar -xvf redmine.tar.gz --strip-components=1 \
#	&& rm redmine.tar.gz files/delete.me log/delete.me \
#	&& mkdir -p tmp/pdf public/plugin_assets \
#	&& chown -R redmine:redmine ./

RUN buildDeps=' \
		gcc \
		libmagickcore-dev \
		libmagickwand-dev \
		libmysqlclient-dev \
		libpq-dev \
		libsqlite3-dev \
		make \
		patch \
	' \
	&& set -ex \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends \
	&& rm -rf /var/lib/apt/lists/* \
	&& bundle install --without development test \
	&& for adapter in mysql2 postgresql sqlite3; do \
		echo "$RAILS_ENV:" > ./config/database.yml; \
		echo "  adapter: $adapter" >> ./config/database.yml; \
		bundle install --without development test; \
	done \
	&& rm ./config/database.yml \
	&& apt-get purge -y --auto-remove $buildDeps


VOLUME /usr/src/redmine

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 3000
CMD ["rails", "server", "-b", "0.0.0.0"]
