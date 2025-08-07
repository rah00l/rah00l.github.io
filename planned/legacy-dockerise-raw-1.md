

21/01/2025 - DOckerise Initiate 

— - - - - - - - - - - — - - - - — - - - — - - - - — - - — — - - - - - - - - - - — - - - - — - - - — - - - - — - - — — - - - - - - - - - - — - - - - — - - - — - - - - — - - — 

Commented test gems, therubyrace, memcatched, dbi, mysql,   installation went upto - see below error -   35.52 Installing sass-rails 4.0.5
36.36 Installing rails 4.1.6
63.20 Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
63.20 
63.20     current directory: /home/appuser/.gem/gems/mysql2-0.3.21/ext/mysql2
63.20 /usr/local/bin/ruby -r ./siteconf20250121-1-kwm824.rb extconf.rb
63.20 checking for ruby/thread.h... yes
63.20 checking for rb_thread_call_without_gvl() in ruby/thread.h... yes
63.20 checking for rb_thread_blocking_region()... no
63.20 checking for rb_wait_for_single_fd()... yes
63.20 checking for rb_hash_dup()... yes
63.20 checking for rb_intern3()... yes
63.20 -----



# Use an official Ubuntu image
FROM ubuntu:20.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Predefine non-interactive environment
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libssl-dev \
    libreadline-dev \
    zlib1g-dev \
    libffi-dev \
    libyaml-dev \
    libgdbm-dev \
    libmysqlclient-dev \
    curl \
    ca-certificates \
    libxml2-dev \
    libxslt1-dev \
    wget \
    tzdata \
    gnupg \
    python2 && \
    ln -s /usr/bin/python2 /usr/bin/python && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Set timezone to avoid interactive prompts
RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

# Install Ruby using ruby-build
RUN curl -fsSL https://github.com/rbenv/ruby-build/archive/refs/heads/master.tar.gz | tar -xz -C /tmp && \
    cd /tmp/ruby-build-* && \
    ./install.sh && \
    ruby-build 2.3.5 /usr/local && \
    rm -rf /tmp/ruby-build-*

# Set Ruby and Bundler globally
RUN ln -s /usr/local/bin/ruby /usr/bin/ruby && \
    ln -s /usr/local/bin/gem /usr/bin/gem && \
    gem install bundler -v 1.17.3

# Fix potential MySQL library paths for Ruby gems
RUN ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so /usr/lib/libmysqlclient.so && \
    ln -s /usr/bin/mysql_config /usr/local/bin/mysql_config

# Add a non-root user
RUN useradd -ms /bin/bash appuser

# Set the working directory
WORKDIR /app

# Change ownership of /app to appuser
RUN chown -R appuser:appuser /app

# Grant permissions to appuser for gem installation
ENV GEM_HOME=/home/appuser/.gem
ENV PATH=$GEM_HOME/bin:$PATH
RUN mkdir -p $GEM_HOME && chown -R appuser:appuser $GEM_HOME

# Switch to the non-root user
USER appuser

# Copy Gemfile and Gemfile.lock for dependency installation
COPY --chown=appuser:appuser Gemfile Gemfile.lock ./

# Configure gem build and install gems
RUN bundle config set --local path "$GEM_HOME" && \
    bundle config set --local build.mysql2 --with-mysql-config=/usr/bin/mysql_config && \
    bundle install --jobs 4

# Copy the application code
COPY --chown=appuser:appuser . .

# Expose the application port
EXPOSE 3000

# Default command
CMD ["bundle", "exec", "rails", "server", "-b", "0.0.0.0"]

— - - - - - - - - - - — - - - - — - - - — - - - - — - - — — - - - - - - - - - - — - - - - — - - - — - - - - — - - — — - - - - - - - - - - — - - - - — - - - — - - - - — - - — 
V0.0.2  - RUN till mysql gem installation - 

# Use an official Ubuntu image
FROM ubuntu:20.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Predefine non-interactive environment
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libssl-dev \
    libreadline-dev \
    zlib1g-dev \
    libffi-dev \
    libyaml-dev \
    libgdbm-dev \
    libmysqlclient-dev \
    curl \
    ca-certificates \
    libxml2-dev \
    libxslt1-dev \
    wget \
    tzdata \
    && rm -rf /var/lib/apt/lists/*

# Set timezone to avoid interactive prompts
RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

# Update config.guess and config.sub
RUN wget -O /usr/share/misc/config.guess http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD && \
    wget -O /usr/share/misc/config.sub http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD

# Install Ruby using ruby-build
RUN curl -fsSL https://github.com/rbenv/ruby-build/archive/refs/heads/master.tar.gz | tar -xz -C /tmp && \
    cd /tmp/ruby-build-* && \
    ./install.sh && \
    ruby-build 2.3.5 /usr/local

# Set Ruby version globally
RUN ln -s /usr/local/bin/ruby /usr/bin/ruby && \
    ln -s /usr/local/bin/gem /usr/bin/gem

# Install Bundler
RUN gem install bundler -v 1.17.3

# Set the working directory
WORKDIR /app

# Copy the application Gemfile
COPY Gemfile Gemfile.lock ./

# Configure MySQL gem build options and install dependencies
RUN bundle config build.mysql --with-mysql-config=/usr/bin/mysql_config && \
    bundle config build.mysql2 --with-mysql-config=/usr/bin/mysql_config && \
    bundle install

# Copy the application code
COPY . .

# Expose the application port
EXPOSE 3000

# Default command
CMD ["bundle", "exec", "rails", "server", "-b", "0.0.0.0"]  docker-compose file -   version: "3.8"

services:
  app:
    build:
      context: .
    volumes:
      - .:/app
    ports:
      - "3000:3000"
    environment:
      DB_HOST: dev-rds01.cfwimw2efneb.us-east-1.rds.amazonaws.com
      DB_USERNAME: sr_web
      DB_PASSWORD: tm2
      DB_NAME: smartrebates


— - - - - - - - - - - — - - - - — - - - — - - - - — - - — — - - - - - - - - - - — - - - - — - - - — - - - - — - - — — - - - - - - - - - - — - - - - — - - - — - - - - — - - — 
V0.0.1  - RUN till mysql gem installation -  
# Use an official Ubuntu image
FROM ubuntu:20.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    libssl-dev \
    libreadline-dev \
    zlib1g-dev \
    ca-certificates \
    libffi-dev \
    wget \
    && rm -rf /var/lib/apt/lists/*

# Update config.guess and config.sub
RUN wget -O /usr/share/misc/config.guess http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD && \
    wget -O /usr/share/misc/config.sub http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD

# Install Ruby using ruby-build
RUN curl -fsSL https://github.com/rbenv/ruby-build/archive/refs/heads/master.tar.gz | tar -xz -C /tmp && \
    cd /tmp/ruby-build-* && \
    ./install.sh && \
    ruby-build 2.3.5 /usr/local

# Set Ruby version globally
RUN ln -s /usr/local/bin/ruby /usr/bin/ruby && \
    ln -s /usr/local/bin/gem /usr/bin/gem

# Install Bundler
RUN gem install bundler -v 1.17.3

# Install application dependencies
COPY Gemfile Gemfile.lock ./
RUN bundle install


— - - - - - - - - - - — - - - - — - - - — - - - - — - - —  — - - - - - - - - - - — - - - - — - - - — - - - - — - - —  — - - - - - - - - - - — - - - - — - - - — - - - - — - - — 
Install Ruby / Gems 

Till - .89 Installing ffi 1.9.18 with native extensions
76.60 Gem::Ext::BuildError: ERROR: Failed to build gem native extension.


# Use an official Ubuntu image
FROM ubuntu:20.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    libssl-dev \
    libreadline-dev \
    zlib1g-dev \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Install Ruby using ruby-build
RUN curl -fsSL https://github.com/rbenv/ruby-build/archive/refs/heads/master.tar.gz | tar -xz -C /tmp && \
    cd /tmp/ruby-build-* && \
    ./install.sh && \
    ruby-build 2.3.5 /usr/local

# Set Ruby version globally
RUN ln -s /usr/local/bin/ruby /usr/bin/ruby && \
    ln -s /usr/local/bin/gem /usr/bin/gem

# Verify Ruby installation
RUN ruby -v && gem -v

# Install Bundler
RUN gem install bundler -v 1.17.3

# Set the working directory
WORKDIR /app

# Copy Gemfile and Gemfile.lock into the container
COPY Gemfile Gemfile.lock ./

# Install Ruby dependencies
RUN bundle install





