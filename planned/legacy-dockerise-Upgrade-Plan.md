
14/02/2025 -- legacy-dockerise-Upgrade-Plan.md


To ensure a smooth upgrade from Ubuntu 16.04 and Ruby 2.3.5 to modern stable versions, we'll take an incremental approach to avoid breaking dependencies. The upgrade will be done in phases, prioritizing backward compatibility and stability.

Upgrade Plan (Step-by-Step)
We'll upgrade incrementally instead of jumping directly to the latest versions.
Phase 1: Upgrade Ubuntu from 16.04 → 18.04
1. Ubuntu 16.04 is EOL, so move to Ubuntu 18.04 (Bionic Beaver), which still supports MySQL 5.7.
2. Ruby 2.3.5 is no longer maintained, but Ubuntu 18.04 can still support it.
3. This phase ensures that the system libraries and dependencies update without breaking Ruby.
Phase 2: Upgrade Ruby from 2.3.5 → 2.5.8
1. Ruby 2.5.8 is the last patch version of Ruby 2.5, which introduces security updates while maintaining compatibility.
2. Ensure compatibility with Bundler and gems (some gems may need updates).
Phase 3: Upgrade Ubuntu from 18.04 → 20.04
1. This moves to Ubuntu 20.04 (Focal Fossa), which introduces updated system packages and dependencies.
2. Verify that MySQL 5.7 remains supported and adjust libraries if needed.
Phase 4: Upgrade Ruby from 2.5.8 → 2.7.8
1. Ruby 2.7.x is the last non-EOL version before Ruby 3.x.
2. Update Bundler and dependencies.
3. Identify gems that will break in Ruby 3.x and find replacements.
Phase 5: Upgrade Ubuntu from 20.04 → 22.04
1. Move to Ubuntu 22.04 (Jammy Jellyfish).
2. Ensure MySQL compatibility (may require moving to MySQL 8).
Phase 6: Upgrade Ruby from 2.7.8 → 3.0.x
1. Ruby 3.0 introduces keyword argument changes that may break gems.
2. Update gems that require compatibility with Ruby 3.0.

Phase 1: Upgrade to Ubuntu 18.04 + Keep Ruby 2.3.5
Let's modify your Dockerfile accordingly.
Updated Dockerfile (Ubuntu 18.04 + Ruby 2.3.5)
# Use Ubuntu 18.04 instead of 16.04
FROM ubuntu:18.04

# Set bash as default shell
SHELL ["/bin/bash", "-c"]

# Predefine non-interactive environment
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies (use updated package names)
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
    software-properties-common \
    g++ && \
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

# Fix MySQL library paths
RUN ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so /usr/lib/libmysqlclient.so && \
    ln -s /usr/bin/mysql_config /usr/local/bin/mysql_config

# Add a non-root user
RUN useradd -ms /bin/bash appuser

# Set working directory
WORKDIR /app

# Set permissions for gem installation
ENV GEM_HOME=/home/appuser/.gem
ENV PATH=$GEM_HOME/bin:$PATH
RUN mkdir -p $GEM_HOME && chown -R appuser:appuser $GEM_HOME

# Switch to non-root user
USER appuser

# Copy Gemfile
COPY --chown=appuser:appuser Gemfile Gemfile.lock ./

# Install Gems
RUN bundle config set --local path "$GEM_HOME" && \
    bundle config set --local build.mysql2 --with-mysql-config=/usr/bin/mysql_config && \
    bundle install --jobs 4

# Copy application code
COPY --chown=appuser:appuser . .

# Expose application port
EXPOSE 3000

# Start Rails server
CMD ["bash", "-c", "rm -f /app/tmp/pids/server.pid && bundle exec rails server -b 0.0.0.0"]

Next Steps:
After successfully building and running with Ubuntu 18.04, we'll proceed with Phase 2: Upgrade Ruby to 2.5.8.
Would you like me to draft the changes for Ruby 2.5.8, or do you want to test this phase first? 🚀
