---
title: Building a Legacy Ruby Environment with Docker Challenges & Solutions
tags: []
style: fill
color: primary
description: Learn how to use Docker to build and manage legacy Ruby environments for older applications. Discover the challenges and solutions when dealing with outdated dependencies and system packages.
---
<br/>
Running legacy applications is a necessary but often frustrating part of software development—especially when those applications rely on outdated technologies. 

Docker offers a great way to isolate and reproduce these old environments. However, setting up a Docker environment for a legacy stack—such as Ruby 2.3.5, Python 2.7, and MySQL 5.7—can present unexpected challenges.

In this guide, I’ll walk you through how we containerized a legacy Ruby app using Docker, including key issues we encountered and how we overcame them.

<img src="{{ site.baseurl }}/public/images/docker-legacy-ruby.png" alt="Legacy Ruby Docker Image">
<br/>

---

### 🧱 The Challenge

Our legacy application relied on:

- **Ruby 2.3.5** (EOL)
- **Python 2.7** (EOL)
- **MySQL 5.7** (soon-to-be deprecated)

Installing and configuring these dependencies on modern systems (like Ubuntu 22.04) is no longer straightforward. Hence, Docker became the obvious choice to freeze the environment and keep everything reproducible.

---

### 🧪 Common Issues We Faced

Here are the top problems we ran into:

1. **Outdated Ruby and Python Versions:**
   - Standard package managers no longer support installing these versions directly.
   - We had to compile Ruby and Python from source.

2. **MySQL Build Compatibility:**
   - The `mysql2` gem requires headers from MySQL 5.7, which aren’t installed by default.
   - We needed to manually symlink MySQL libraries to ensure compatibility.

3. **Gem Installation Fails:**
   - Modern versions of `bundler` aren’t compatible with older Ruby versions.
   - We locked `bundler` to version `1.17.3`.

4. **Timezone Configuration:**
   - Modern containers prompt for timezones interactively; we had to bypass that with `DEBIAN_FRONTEND=noninteractive`.

5. **User Permissions:**
   - Installing gems as `root` inside the container caused permission issues.
   - We added a non-root user and adjusted paths for `GEM_HOME`.

---

### 🛠️ The Dockerfile (With Fixes)

```dockerfile
FROM ubuntu:16.04
SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND=noninteractive

# Install OS-level dependencies
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

# Install Python 2.7
RUN curl -fsSL https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz -o /tmp/python2.tar.gz && \
    tar -xzf /tmp/python2.tar.gz -C /tmp && \
    cd /tmp/Python-2.7.18 && \
    ./configure --enable-optimizations && \
    make -j"$(nproc)" && \
    make altinstall && \
    rm -f /usr/bin/python2 && \
    ln -s /usr/local/bin/python2.7 /usr/bin/python2 && \
    ln -s /usr/local/bin/pip2 /usr/bin/pip2 && \
    rm -rf /tmp/Python-2.7.18 /tmp/python2.tar.gz

# Set timezone
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

# Fix MySQL header paths
RUN ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so /usr/lib/libmysqlclient.so && \
    ln -s /usr/bin/mysql_config /usr/local/bin/mysql_config

# Create app user
RUN useradd -ms /bin/bash appuser
WORKDIR /app
RUN chown -R appuser:appuser /app

# Set GEM_HOME for appuser
ENV GEM_HOME=/home/appuser/.gem
ENV PATH=$GEM_HOME/bin:$PATH
RUN mkdir -p $GEM_HOME && chown -R appuser:appuser $GEM_HOME

# Switch to non-root user
USER appuser

# Copy Gemfile before copying full source
COPY --chown=appuser:appuser Gemfile Gemfile.lock ./

# Install gems with mysql2 fix
RUN bundle config set --local path "$GEM_HOME" && \
    bundle config set --local build.mysql2 --with-mysql-config=/usr/bin/mysql_config && \
    bundle install --jobs 4

# Copy source code
COPY --chown=appuser:appuser . .

EXPOSE 3000
CMD ["bundle", "exec", "rails", "server", "-b", "0.0.0.0"]
```

---

### 🧠 Key Takeaways

- **Docker is a lifesaver** for running legacy apps on modern systems.
- **Don’t rely on apt for older runtimes**—you’ll likely need to compile from source.
- **Use proper user management and environment paths** to avoid permission issues.
- **Pin your Bundler and Ruby versions tightly** to match your app’s original setup.

---

#### Running Your App with Docker – Key Commands

Once you've structured and built your Docker environment, here are the key commands you'll use to get your application up and running from your local system:


**🛠️ `docker-compose build`**  

This command builds the images defined in your `docker-compose.yml` file. It reads the configuration and uses the provided Dockerfiles to construct Docker images for each service. It’s essential for preparing your environment before you can run containers.

```bash
docker-compose build
```

**Significance:**  

This ensures your Docker containers are built based on the latest code and dependencies defined in the project. Use it whenever you make updates to the Dockerfile or dependencies in your app.

---

**🚀 `docker run -it --rm -p 3000:3000 inn-command-center-app /bin/bash`**

```bash
docker run -it --rm -p 3000:3000 inn-command-center-app /bin/bash
```

**Significance:**  

- `-it`: Runs the container in interactive mode so you can interact with the terminal.
- `--rm`: Automatically removes the container after it exits, keeping your system clean.
- `-p 3000:3000`: Maps port 3000 inside the container to port 3000 on your local machine, making your app accessible at `localhost:3000`.
- `inn-command-center-app`: The name of the Docker image you built.
- `/bin/bash`: Opens a bash shell inside the container.

**Why this matters:**  
This is how you jump into your app's Docker container and run commands, start the Rails server, or troubleshoot issues interactively—all while isolating your local environment.

---

Adding these commands know the steps to spin up your app.

By solving these containerization hurdles, we’ve ensured our legacy Ruby app can run reliably for years to come—isolated from host dependencies and easier to maintain across dev environments.

🚀 If you're dealing with a legacy Ruby setup, feel free to fork this Dockerfile and tweak it for your needs. 
Happy hacking!

