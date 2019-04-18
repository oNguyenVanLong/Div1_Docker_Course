FROM ruby:2.6.3

WORKDIR /usr/src/app

RUN apt-get update -qq && apt-get install -y nodejs postgresql-client
RUN gem install rails

COPY Gemfile* ./
RUN bundle

EXPOSE 3000
