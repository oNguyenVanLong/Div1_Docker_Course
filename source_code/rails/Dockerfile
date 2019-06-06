FROM ruby:2.5.1

# Install apt based dependencies required to run Rails as
# well as RubyGems.
RUN apt-get update && apt-get install -y \
  build-essential \
  nodejs \
  vim cron

# Configure the main working directory. This is the base
# directory used in any further RUN, COPY, and ENTRYPOINT
# commands.
RUN mkdir -p /app
WORKDIR /app

# setting timezone (Asia/Tokyo)
ENV TZ JST-9

# Copy the Gemfile as well as the Gemfile.lock and install
# the RubyGems. This is a separate step so the dependencies
# will be cached unless changes to one of those two files
# are made.
COPY Gemfile Gemfile.lock ./
RUN bundle install --jobs 20 --retry 5

EXPOSE 3000

