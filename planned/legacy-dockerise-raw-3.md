
25/01/2025 -- legacy-dockerise-raw-3.md

Here’s another version of the content, refined and slightly restructured for clarity and future reference:

Transitioning to Docker: Lessons Learned and Implementation Details

The Need for Docker
As the application scaled and the team diversified, maintaining a consistent environment across development, staging, and production became increasingly challenging. Docker was chosen to encapsulate the entire application environment, ensuring uniformity, reproducibility, and faster onboarding for new developers.

Challenges During Migration
1. Handling Legacy Dependencies
    * The application relied on older versions of Ruby (2.3.5), Python (2.7), and MySQL (5.7). Installing and configuring these versions on modern systems proved challenging.
2. Complex Gem Dependencies
    * Gems like mysql2 required additional build configurations and dependencies, such as libmysqlclient-dev, which needed specific paths and settings.
3. Non-Root User Configuration
    * For security, running the application under a non-root user was essential. Configuring file permissions and environment paths to accommodate this setup took extra effort.
4. Timezone and Locale Settings
    * Ensuring consistent timezone settings in the container while avoiding interactive prompts during builds was a critical consideration.
5. Dynamic Development Workflow
    * Integrating Docker with live code changes required proper volume mapping to sync code updates without rebuilding the image.

Dockerfile: The Core of the Solution
The Dockerfile was crafted to address these challenges systematically.
Key Highlights
1. Base Image Selection
    * Chose ubuntu:16.04 for compatibility with MySQL 5.7 and other legacy components.
2. Dependency Installation
    * Installed essential libraries and tools such as libmysqlclient-dev, libxml2-dev, and g++ to support Ruby and gem compilation.
3. Python 2.7 Installation
    * Downloaded and manually compiled Python 2.7.18 to meet compatibility requirements.
4. Ruby and Bundler Configuration
    * Used ruby-build to install Ruby 2.3.5.
    * Installed Bundler version 1.17.3 for compatibility with older Rails projects.
5. Non-Root User Setup
    * Created an appuser with appropriate ownership and permissions for application files and gem paths.
6. Optimized Gem Installation
    * Configured bundle to:
        * Use a custom installation path ($GEM_HOME).
        * Build the mysql2 gem with specific flags to locate mysql_config.
7. Final Setup and Port Exposure
    * Set up the working directory, copied application files, and exposed port 3000 for the Rails server.

# Dockerfile content (refer to provided file)

# Use an older Ubuntu base image (16.04) that supports MySQL 5.7
FROM ubuntu:16.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Predefine non-interactive environment
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies including g++ for compiling Python 2.7
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
    python-software-properties \
    g++ && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Python 2.7 manually
RUN curl -fsSL https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz -o /tmp/python2.tar.gz && \
    tar -xzf /tmp/python2.tar.gz -C /tmp && \
    cd /tmp/Python-2.7.18 && \
    ./configure --enable-optimizations && \
    make -j"$(nproc)" && \
    make altinstall && \
    # Remove existing symbolic links if they exist
    rm -f /usr/bin/python2 && \
    rm -f /usr/bin/pip2 && \
    # Create new symbolic links
    ln -s /usr/local/bin/python2.7 /usr/bin/python2 && \
    ln -s /usr/local/bin/pip2 /usr/bin/pip2 && \
    rm -rf /tmp/Python-2.7.18 /tmp/python2.tar.gz

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


Docker Compose: Simplifying Multi-Container Setup
The docker-compose.yml file made managing the application container straightforward.
Key Configurations
* Volume Mapping
    * Mapped the host machine’s application directory to the container for seamless development updates.
* Environment Variables
    * Configured database credentials (DB_HOST, DB_USERNAME, DB_PASSWORD, DB_NAME) to avoid hardcoding sensitive details in the codebase.
* Port Mapping
    * Exposed the Rails application on port 3000 for external access.
# docker-compose.yml content (refer to provided file)
#version: "3.8"

services:
  app:
    build:
      context: .
    volumes:
      - .:/app
    ports:
      - "3000:3000"
    environment:
      DB_HOST: 
      DB_USERNAME: sr_web
      DB_PASSWORD: tm2
      DB_NAME: smartrebates


Resolving Gem and Dependency Issues
1. Gemfile Locking
    * Maintained a consistent environment by ensuring all gems were locked to compatible versions in Gemfile.lock.
2. mysql2 Gem Configuration
    * Resolved MySQL library issues by explicitly setting the build flag: bundle config set --local build.mysql2 --with-mysql-config=/usr/bin/mysql_config
    * 
3. Bundler Custom Path
    * Set gems to install in a user-specific directory to avoid permission issues: bundle config set --local path "$GEM_HOME"
# Gemfile (refer to provided file) source 'https://rubygems.org'


# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '4.1.6'
#gem 'rails', '4.1.1'
#gem 'rails', '4.0.2'
# Use sqlite3 as the database for Active Record
#gem 'sqlite3'
# Use SCSS for stylesheets
gem 'sass-rails', '~> 4.0.3'
# Use Uglifier as compressor for JavaScript assets
gem 'uglifier', '>= 1.3.0'
# Use CoffeeScript for .js.coffee assets and views
gem 'coffee-rails', '~> 4.0.0'
# See https://github.com/sstephenson/execjs#readme for more supported runtimes
# gem 'therubyracer',  platforms: :ruby

# Use jquery as the JavaScript library
gem 'jquery-rails'
# Turbolinks makes following links in your web application faster. Read more: https://github.com/rails/turbolinks
gem 'turbolinks'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
gem 'jbuilder', '~> 2.0'
# bundle exec rake doc:rails generates the API under doc/api.
gem 'sdoc', '~> 0.4.0', group: :doc

# Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
gem 'spring',        group: :development

# Use ActiveModel has_secure_password
# gem 'bcrypt', '~> 3.1.7'

# Use unicorn as the app server
# gem 'unicorn'

# Use Capistrano for deployment
gem 'capistrano', group: :development
#gem 'capistrano-rails', group: :development

# Use debugger
#gem 'debugger', group: [:development, :test]

gem 'ruby-debug-passenger'

# gem 'therubyracer'
# gem 'mini_racer', '~> 0.2.4' #Use it if application not works in dev env

gem 'mini_racer', platforms: :ruby
# gem 'libv8', '3.16.14.19', platforms: :ruby

# server gem
gem 'passenger', '~> 4.0.45'

# Gems from old rails 2 code
#

gem 'atomic'
gem 'aws-sdk-s3', require: false 
gem 'bcrypt', '~> 3.1.7'
gem 'browser'
gem 'childprocess'
gem 'composite_primary_keys', '~> 7.0.11'
#gem 'crontab-parser'
# gem 'dbd-mysql'
# gem 'dbi'
gem 'deprecated', "~> 2.0.1"
gem 'ffi'
gem 'htmlfilter'
gem 'iconv'
# gem 'libv8', '3.16.14.19', require: false
# gem 'therubyracer', '0.12.3'
gem 'macaddr'
# gem 'memcached'
#gem 'mysql2'
gem 'mysql2', '~> 0.3.18'
gem 'open4'
gem 'ref'
gem 'ruby-ole'
gem 'rubyzip', "~> 1.0"     # selenium-webdriver 2.45.0 depends on ~> 1.0
gem 'spreadsheet'
gem 'strip_attributes'	# /vendor/plugins discouraged as of Rails 4 (using this as a gem - https://rubygems.org/gems/strip_attributes)	
# the following gems are no longer used.
#gem 'svn_wc'
#gem 'svn_wc_tree'
gem 'systemu'
gem 'thread_safe'	#	probably not required, as ruby 2 is by default thread safe
gem 'user_agent_parser'
gem 'uuid'
gem 'websocket'
gem 'sanitize'
gem 'american_date'
gem 'safe_cookies'		# https://github.com/makandra/safe_cookies - httponly and secure
gem 'non-stupid-digest-assets'  # until we convert all the Perl app to Rails4, we need this gem to serve assets for both Perl and Rails in prod
# Rails 4 by default has support for Minitest::Spec (Rspec style of testing) with fixtures and is much faster than using Rspecs.
=begin
group :development, :test do
  gem 'rspec'
  gem 'rspec-rails', '~> 3.0.0'
  gem 'jasmine'
  gem 'jasmine-rails'
  gem 'guard'
  gem 'guard-rspec', require: false
end
=end


gem 'js-routes', '~> 0.9.5'
gem 'responders'

# Zendesk API for creating tickets from Data Entry (missing transaction info - #9737758)
gem 'zendesk_api'

# becase CGI.unescpaeHTML isn't very good
gem 'htmlentities'

# re https://we-like-it.atlassian.net/browse/INRCP-67
gem 'fastimage'

# https://we-like-it.atlassian.net/browse/INRCP-1017
# revist in future
#gem 'makara'

# group :development, :test do
#   gem 'minitest-rails-capybara'
#   gem 'meta_request'
#   gem 'guard-minitest'
#   gem 'factory_girl', '~> 4.4.0'
#   gem 'factory_girl_rails', '~> 4.4.1'
#   gem 'rails4_upgrade'
#   #gem 'bullet'
#   # to support old activerecord queries -- this needs to go away
#   #gem 'activerecord-deprecated_finders', require: 'active_record/deprecated_finders'
#   gem 'rails-erd'
#   gem 'railroady'

#   gem 'minitest', '~> 5.3.5'
#   #gem 'minitest-rails', git: "git://github.com/blowmage/minitest-rails.git"
#   gem 'minitest-rails', '~> 2.0.3'
#   gem 'headless'                  # have it work with Xvfb.
#   gem 'watir-webdriver', '0.6.11'
#   gem 'selenium-webdriver', '2.45.0'
#   gem 'parallel_tests'
#   gem 'minitest-reporters'
# end


Benefits of Dockerization
1. Environment Consistency
    * Every developer now works in an identical environment, eliminating "works on my machine" issues.
2. Simplified Onboarding
    * New developers or contributors can spin up the application within minutes by running docker-compose up.
3. Enhanced Security
    * Running the application under a non-root user minimizes security risks.
4. Future-Proofing
    * The containerized setup ensures that even as host operating systems evolve, the application remains isolated and functional.

Final Reflection
Migrating the application to Docker was a transformative process. Despite the initial challenges, the end result is a more maintainable, portable, and secure setup. This documentation captures the key steps and decisions, serving as a reference for future migrations or updates.
By leveraging Docker effectively, we have paved the way for smoother development workflows and more reliable deployments.

This version provides a structured narrative with a focus on both the challenges faced and the solutions implemented during the Docker migration process. It’s concise but informative enough to serve as a reference for similar future projects. Let me know if you'd like further tweaks!


- - - - - - - - - - — - - — - - - — - - —  - - — — - - - - — - — - - - - - - - - - - - - - - - — - - — - - - — - - —  - - — — - - - - — - — - - - - - -


Certainly! Below is another response tailored to your request for proper content regarding transitioning your application setup to Docker, challenges faced, and how the dependencies and configuration files (Dockerfile, docker-compose, Gemfile) were handled for future reference.

Transitioning to Docker: Challenges and Solutions
Why Docker?
As our application grew in complexity and we needed to ensure consistent setups across multiple environments, Docker provided the perfect solution. Docker allowed us to encapsulate the application along with its dependencies into portable containers. This ensured that our development, staging, and production environments were uniform.
Key Challenges Faced
1. Legacy Dependencies:
    * The application used older versions of Ruby (2.3.5), Python (2.7), and MySQL (5.7), which required careful attention to maintain compatibility.
    * Many dependencies were either deprecated or had specific build requirements that needed resolution.
2. Dependency Resolution:
    * Gems like mysql2 required custom build flags to locate the mysql_config file.
    * Managing Bundler's path configuration in a containerized environment was non-trivial.
3. Non-Root User Setup:
    * For security reasons, the application needed to run under a non-root user, which introduced challenges in ensuring permissions were correctly configured for gem installations.
4. Time Zone Configuration:
    * Avoiding interactive timezone prompts during container build required preconfiguring tzdata.

Solution: Dockerfile Configuration
The Dockerfile was meticulously crafted to address the above challenges. Below is an explanation of its structure:
1. Base Image and Shell:
    * Used ubuntu:16.04 to maintain compatibility with MySQL 5.7 and set bash as the default shell.
2. Dependency Installation:
    * Installed build tools and libraries (libmysqlclient-dev, g++, etc.) essential for compiling older Ruby and Python versions.
3. Python 2.7 Installation:
    * Manually compiled Python 2.7.18 since it was no longer available via default package managers.
4. Ruby Installation:
    * Leveraged ruby-build to install Ruby 2.3.5.
5. Gem and Bundler Setup:
    * Configured Bundler to install gems in a non-root directory.
    * Resolved MySQL gem dependency using custom build flags.
6. Non-Root User:
    * Created an appuser with ownership of the application directory and gem paths.
7. Gemfile Integration:
    * Copied Gemfile and Gemfile.lock for dependency installation during the build process.
8. Application Setup:
    * Ensured the application directory was owned by appuser.
    * Exposed port 3000 for the Rails server.
# Dockerfile content (refer to provided file)
# Use an older Ubuntu base image (16.04) that supports MySQL 5.7
FROM ubuntu:16.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Predefine non-interactive environment
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies including g++ for compiling Python 2.7
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
    python-software-properties \
    g++ && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Python 2.7 manually
RUN curl -fsSL https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz -o /tmp/python2.tar.gz && \
    tar -xzf /tmp/python2.tar.gz -C /tmp && \
    cd /tmp/Python-2.7.18 && \
    ./configure --enable-optimizations && \
    make -j"$(nproc)" && \
    make altinstall && \
    # Remove existing symbolic links if they exist
    rm -f /usr/bin/python2 && \
    rm -f /usr/bin/pip2 && \
    # Create new symbolic links
    ln -s /usr/local/bin/python2.7 /usr/bin/python2 && \
    ln -s /usr/local/bin/pip2 /usr/bin/pip2 && \
    rm -rf /tmp/Python-2.7.18 /tmp/python2.tar.gz

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


Docker Compose Setup
The docker-compose.yml file facilitated container orchestration. Key points include:
* Volume mapping ensured code changes were instantly reflected in the container during development.
* Environment variables configured database connection parameters for the Rails application.
# docker-compose.yml content (refer to provided file)
#version: "3.8"

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



Gemfile Challenges and Solutions
The Gemfile had several dependencies that required careful resolution:
1. Dependency Compatibility:
    * Ensured gem versions were compatible with Ruby 2.3.5.
    * Used Gemfile.lock to lock down exact versions to avoid mismatches.
2. MySQL Gem Installation:
    * Used bundle config to set build flags for the mysql2 gem: bundle config set --local build.mysql2 --with-mysql-config=/usr/bin/mysql_config
    * 
3. Bundler Path Setup:
    * Configured gems to install in a custom path: bundle config set --local path "$GEM_HOME"
    * 

Final Reflection
Transitioning the application to Docker was a significant but rewarding process. The challenges of dealing with legacy dependencies, setting up non-root users, and ensuring a seamless development experience were all mitigated by careful planning and the creation of a robust Dockerfile and docker-compose setup.
This documentation serves as a detailed guide for future migrations or troubleshooting containerized environments.

- - - - - - - - - - — - - — - - - — - - —  - - — — - - - - — - — - - - - - - - - - - - - - - - — - - — - - - — - - —  - - — — - - - - — - — - - - - - -


Here's a detailed explanation of how I transitioned to using Docker for setting up an application, the challenges I faced, and the solutions I implemented. This documentation also includes a breakdown of the Dockerfile, docker-compose.yml, and Gemfile configuration, along with steps to resolve Gemfile dependencies.

Transitioning to Docker: Challenges and Solutions
Initial Steps
1. Requirement for Dockerization:
    * Simplify the setup process for local development and onboarding new developers.
    * Ensure consistency between development, staging, and production environments.
2. Selection of Base Image:
    * Chose ubuntu:16.04 to match the compatibility requirements of MySQL 5.7 and Ruby 2.3.5.
    * Ensured that the base image supported libraries and dependencies needed for the legacy application.

Challenges Faced
1. Python 2.7 Installation
* Legacy dependencies required Python 2.7, which was not readily available in the base image.
* Solution: Manually installed Python 2.7 using a custom script, ensuring it worked seamlessly with the existing tools.
2. Ruby and Gem Compatibility
* Ruby 2.3.5 required specific patches and configurations, especially for bundler and MySQL-related gems.
* Solution: Used ruby-build to compile Ruby from source and configured bundler with MySQL-specific build flags.
3. MySQL Library Path Issues
* The mysql2 gem failed during installation due to incorrect library paths.
* Solution: Created symbolic links for libmysqlclient.so and mysql_config to fix the issue.
4. Permission Issues for Non-Root User
* Running the application as a non-root user (appuser) required proper permission setup for gem installations.
* Solution: Set the GEM_HOME and adjusted ownership and permissions accordingly.
5. Gemfile Dependencies
* Some gems required system-level dependencies and specific flags during installation.
* Solution: Pre-installed required libraries and configured bundler to include necessary flags (build.mysql2).
6. Dynamic Environment Configuration
* Managing sensitive credentials and environment variables for database connections.
* Solution: Used environment variables in the docker-compose.yml file to configure the application dynamically.

Final Docker Configuration
Dockerfile
# Use an older Ubuntu base image (16.04) that supports MySQL 5.7
FROM ubuntu:16.04

# Set bash as the default shell
SHELL ["/bin/bash", "-c"]

# Predefine non-interactive environment
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies including g++ for compiling Python 2.7
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
    python-software-properties \
    g++ && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Python 2.7 manually
RUN curl -fsSL https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz -o /tmp/python2.tar.gz && \
    tar -xzf /tmp/python2.tar.gz -C /tmp && \
    cd /tmp/Python-2.7.18 && \
    ./configure --enable-optimizations && \
    make -j"$(nproc)" && \
    make altinstall && \
    # Remove existing symbolic links if they exist
    rm -f /usr/bin/python2 && \
    rm -f /usr/bin/pip2 && \
    # Create new symbolic links
    ln -s /usr/local/bin/python2.7 /usr/bin/python2 && \
    ln -s /usr/local/bin/pip2 /usr/bin/pip2 && \
    rm -rf /tmp/Python-2.7.18 /tmp/python2.tar.gz

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

Docker-compose.yml
version: "3.8"

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

Resolving Gemfile Dependencies
1. Gemfile
    * Added necessary gems with specific versions to ensure compatibility.
source 'https://rubygems.org'

gem 'rails', '4.2.10'
gem 'mysql2', '~> 0.4.10'
gem 'bundler', '~> 1.17'
2. Gem Installation
    * Resolved dependency issues by setting build flags in the Dockerfile: bundle config set --local build.mysql2 --with-mysql-config=/usr/bin/mysql_config
    * 

Conclusion
With the final setup, the Docker-based environment enabled seamless application deployment and eliminated environment-specific issues. This documentation serves as a reference for replicating the setup or addressing similar challenges in the future.
