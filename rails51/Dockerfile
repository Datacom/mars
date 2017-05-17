FROM ubuntu:xenial
MAINTAINER 244 MARS Wellington <mars@datacom.co.nz>

ARG BUILD_DATE
ARG GIT_REF

LABEL org.label-schema.schema-version="1.0" \
      org.label-schema.name="Mars Base for Rails 5.1 with Ruby 2.4, Node 6, PG 9.6" \
      org.label-schema.git-url="https://github.com/datacom/mars/rails51" \
      org.label-schema.git-ref="${VCS_REF}" \
      org.label-schema.build-date="${BUILD_DATE}"

ENV RUBY_VERSION=2.4 \
    RAILS_ENV=production \
    NODE_ENV=production \
    INSTALL_PATH=/app \
    DEBIAN_FRONTEND=noninteractive \
    BUNDLE_SILENCE_ROOT_WARNING=1

RUN echo 'APT::Install-Recommends 0;' >> /etc/apt/apt.conf.d/01norecommends \
 && echo 'APT::Install-Suggests 0;' >> /etc/apt/apt.conf.d/01norecommends \
 && apt-get update && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    net-tools \
    tzdata \
    unzip \
    curl \
    nano

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E1DD270288B4E6030699E45FA1715D88E1DF1F24 \
 && echo "deb http://ppa.launchpad.net/git-core/ppa/ubuntu xenial main" >> /etc/apt/sources.list \
 && apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 80F70E11F0F0D5F10CB20E62F5DA5F09C3173AA6 \
 && echo "deb http://ppa.launchpad.net/brightbox/ruby-ng/ubuntu xenial main" >> /etc/apt/sources.list \
 && curl -sL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
 && echo 'deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main' > /etc/apt/sources.list.d/pgdg.list \
 && curl -sL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add - \
 && echo 'deb https://deb.nodesource.com/node_6.x xenial main' > /etc/apt/sources.list.d/nodesource.list \
 && curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
 && echo 'deb https://dl.yarnpkg.com/debian/ stable main' > /etc/apt/sources.list.d/yarn.list \
 && apt-get update \
 && apt-get -yy upgrade

RUN apt-get install -y \
    git-core \
    libpq5 \
    libpq-dev \
    libev-dev \
    libffi-dev \
    libgdbm3 \
    libssl-dev \
    libyaml-dev \
    procps \
    zlib1g-dev \
    libreadline6 \
    libncurses5 \
    locales \
    nodejs \
    postgresql-client \
    ruby${RUBY_VERSION} \
    ruby${RUBY_VERSION}-dev \
    ruby-dev \
    gcc \
    make \
    yarn \
 && find /usr/share/locale -mindepth 1 -maxdepth 1 ! -name 'en' | xargs rm -r 



# native gems
# nokogiri 1.7.2
# sqlite3
# puma 3.8.2


# ruby-nio4r 1.1.0


# Prepare OS
RUN update-locale LANG=C.UTF-8 LC_MESSAGES=POSIX \
 && locale-gen en_US.UTF-8 \
 && dpkg-reconfigure locales

RUN gem install bundler --no-ri --no-rdoc

# Setup application install path
RUN mkdir -p $INSTALL_PATH
WORKDIR $INSTALL_PATH

COPY Gemfile Gemfile.lock ./
RUN bundle install --binstubs --without development test

COPY . .

RUN yarn install --pure-lockfile

# Precompile with dummy values in valid syntax
RUN bundle exec rake RAILS_ENV=production DATABASE_URL=postgresql://user:pass@127.0.0.1/dbname ACTION_CABLE_ALLOWED_REQUEST_ORIGINS=foo,bar SECRET_TOKEN=dummytoken assets:precompile

EXPOSE 3000/tcp

CMD rails server
