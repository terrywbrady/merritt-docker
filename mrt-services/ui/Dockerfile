#*********************************************************************
#   Copyright 2019 Regents of the University of California
#   All rights reserved
#*********************************************************************
# See https://itnext.io/docker-rails-puma-nginx-postgres-999cd8866b18
#   docker build -t cdluc3/mrt-dashboard .

FROM ruby:2.4.4
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs

# Set an environment variable where the Rails app is installed to inside of Docker image
ENV RAILS_ROOT /var/www/app_name
RUN mkdir -p $RAILS_ROOT

# Set working directory
WORKDIR $RAILS_ROOT

# Setting env up
ENV RAILS_ENV='test'
ENV RACK_ENV='test'

# Adding gems
COPY mrt-dashboard/Gemfile Gemfile
COPY mrt-dashboard/Gemfile.lock Gemfile.lock

RUN bundle install --jobs 20 --retry 5
# Adding project files

COPY mrt-dashboard .
COPY mock-ldap.yml config/ldap.yml
COPY mock-atom.yml config/atom.yml
COPY database.yml.example config/database.yml
COPY mock-app_config.yml config/app_config.yml

RUN bundle exec rake assets:precompile
EXPOSE 3000 9292
CMD ["bundle", "exec", "puma", "-C", "config/application.rb"]
