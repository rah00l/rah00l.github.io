

23/01/2025 -- 


 => => # ==> Installing ruby-2.3.5...                                                                                                                                                   
 => => # WARNING: ruby-2.3.5 is past its end of life and is now unsupported.                                                                                                            
 => => # It no longer receives bug fixes or critical security updates.  


Rosetta 2 Exploration
arch switch homebrew switch  need to use it needed to arch x86_64 instead on arm64  rvm install 
openssl  1.1  - Nautanki not available for old version done tap - 






Recap of R&D/Analysis & Troubleshooting for Running Legacy Web App on M1 Mac


Objective: Run/Make old legacy web application work on Mac with M1 chip, using:
* Ruby 2.2.2 / Ruby 2.3.3
* Rails 4.1.6
* MySQL 5.7.x (due to compatibility with mysql2 gem v0.3.18)
Key Challenges Encountered:
1. Architecture Compatibility: The legacy application and its dependencies were built for Intel (x86_64) architecture, but the M1 Mac uses ARM64 architecture. This caused compatibility issues during the build process.
2. Libffi Compatibility: The libffi library (version 3.4) caused compilation errors with the application, as it's not compatible with the version required by older Ruby versions.
3. Outdated Dependencies:
    * Ruby 2.3.3 (and related dependencies) are not easily compatible with newer tools on M1.
    * mysql2 gem is tied to MySQL 5.7.x, limiting version upgrades due to its dependency on the old Ruby version.
Troubleshooting Steps:
1. Attempt to Downgrade libffi: Tried installing libffi 3.3 manually, but encountered architecture compatibility issues during the make process (undefined symbols for ARM64).
2. Rosetta 2 Exploration: Considered using Rosetta 2 to run the legacy application in Intel (x86_64) mode, which would bypass the architecture conflicts with the current dependencies.
Final Solution:
1. Use Rosetta 2 for Legacy Compatibility:
    * Run the application and build dependencies (Ruby, MySQL, gems) in Intel (x86_64) mode using Rosetta 2.
    * This allows running the older dependencies without having to rebuild them for ARM64.
    * Steps:
        * Install Ruby and MySQL 5.7 under Rosetta 2 mode using arch -x86_64.
        * Install required gems and dependencies in this mode to avoid architecture conflicts.
2. Configuration:
    * Set Ruby 2.3.3 using rbenv local 2.3.3.
    * Ensure MySQL 5.7 is installed and linked correctly using Homebrew.
    * Configure mysql2 gem to link with MySQL 5.7 using custom mysql_config.
3. Future Consideration:
    * Continue using Rosetta 2 for legacy applications as a short-term solution.
    * Plan to eventually migrate to newer Ruby and Rails versions to reduce reliance on Rosetta 2 and ensure long-term stability and performance.
This approach has allowed the legacy application to run smoothly on the M1 Mac, while preserving the compatibility of the old dependencies.



- - - - - - - - - - — - - — - - - — - - —  - - — — - - - - — - — - - - - - - - - - - - - - - - — - - — - - - — - - —  - - — — - - - - — - — - - - - - -


To get your legacy web application running on a Mac with M1 while using Ruby 2.3.3, Rails 4.1.6, MySQL 5.7.x, and older library dependencies, there are several key challenges you'll need to address, especially around architecture compatibility (ARM64 vs. Intel) and outdated dependencies.

Here's a breakdown of what you should keep in mind and the necessary steps for a smooth transition:

1. Key Challenges for Legacy Web Apps on M1 Macs:
* Architecture Compatibility: Apple's M1 chip uses ARM64 architecture, which is different from Intel's x86_64 architecture that many legacy applications and libraries were built for. Since many of the libraries, especially in your Ruby environment (like mysql2 gem or Ruby itself), were likely compiled for Intel, you might encounter architecture-related issues.
* Outdated Ruby Version (2.3.3): Ruby 2.3.3 is quite old, and it might not support newer versions of libraries or even work well with M1 Macs natively. Ruby 2.4 and above unified integer types, which causes compatibility issues with the mysql2 gem version 0.3.18 you're using.
* Outdated MySQL Version (5.7.x): Since you are tied to MySQL 5.7.x, installing and configuring it on a newer M1 system may pose some compatibility challenges, particularly with certain gems like mysql2.
* Gem Compatibility (e.g., mysql2, Rails 4.1.6): Some gems and libraries in your app (such as mysql2, which has specific version restrictions for Ruby) may not be easily installed or compatible with newer systems.

2. Strategy to Get Legacy Web App Running on M1 Mac:
A. Use Rosetta 2 to Run Legacy Applications in x86_64 Mode:
Since your application dependencies are built for Intel architecture (x86_64), using Rosetta 2 allows you to run these dependencies without modifying or rebuilding them for ARM64.
1. Enable Rosetta 2:
    * To run terminal commands under Rosetta 2, you can execute them in x86_64 mode using the following command: arch -x86_64 /usr/bin/env bash
    * 
2. Install Dependencies (like mysql@5.7 and other legacy tools):
    * After switching to Rosetta mode, you can try running brew install mysql@5.7 and other dependencies.
3. Run rbenv in Rosetta Mode:
    * When working with Ruby, it's crucial to install Ruby versions using Rosetta as well to avoid architecture conflicts: arch -x86_64 rbenv install 2.3.3
    * 
4. Install Gems Using Bundler:
    * Ensure that all your gems (like rails, mysql2) are installed in the Rosetta 2 environment: arch -x86_64 gem install bundler
    * arch -x86_64 bundle install
    * 

B. Alternative: Build Dependencies for ARM64:
If you'd like to avoid using Rosetta 2 in the long term (which could affect performance), you can attempt to rebuild the necessary dependencies for ARM64 (Apple Silicon) using Homebrew and other tools.
1. Install ARM64-compatible MySQL:
    * On M1 Macs, MySQL 5.7 might work in ARM64 mode directly. Try installing MySQL 5.7 in ARM64: brew install mysql@5.7
    * 
2. Rebuild Ruby for ARM64:
    * Instead of using the x86_64 architecture, you can try rebuilding Ruby 2.3.3 for ARM64, although it's an older version: arch -arm64 rbenv install 2.3.3
    * 
3. Update Gems (if possible):
    * Consider updating any gems that may have ARM64-compatible versions or newer versions that support Apple Silicon. Sometimes, older gems like mysql2 might require specific versions for compatibility.

C. Configure Legacy Database & Libraries:
Given that MySQL 5.7 is a key requirement, ensure your MySQL configuration (in both Rosetta and ARM64 modes) is properly set up:
1. Configure MySQL 5.7:
    * Add the appropriate path to MySQL binaries (as you did in .zshrc or .bashrc): export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"
    * 
2. Reconfigure mysql2 Gem:
    * Ensure the mysql2 gem is correctly linked to your MySQL 5.7 installation: bundle config build.mysql2 --with-mysql-config=/usr/local/opt/mysql@5.7/bin/mysql_config
    * 

3. Key Configuration Steps:
1. Ruby Version:
    * Use rbenv to set Ruby 2.3.3 as the local version: rbenv local 2.3.3
    * 
2. Install Bundler:
    * Install Bundler if not already done: gem install bundler
    * 
3. Install MySQL:
    * Install MySQL 5.7 using Homebrew in Rosetta 2 mode or ARM64 mode: brew install mysql@5.7
    * 
4. Bundle Install:
    * Run bundle install to install all your app's dependencies: bundle install
    * 

4. Considerations Going Forward:
While Rosetta 2 offers a convenient solution to run legacy software, it's not a long-term fix. As you continue working on your legacy application, consider:
* Gradually Upgrading: Over time, try updating your app to support newer Ruby and Rails versions. This will help ensure compatibility with modern libraries and operating systems, reducing the reliance on legacy architecture support like Rosetta.
* Exploring Alternative Solutions: If Rosetta 2 doesn't provide the performance or stability you need, consider moving to a Docker-based solution, where you can encapsulate your entire app environment (including Ruby, MySQL, etc.) in a container.

Summary:
* Use Rosetta 2 to run Intel-based binaries (like Ruby 2.3.3, MySQL 5.7) on M1 Macs.
* Alternatively, attempt to rebuild dependencies natively for ARM64.
* Ensure MySQL 5.7 and the mysql2 gem are properly configured for your environment.
* Gradually update the legacy app to avoid long-term dependence on Rosetta.
By following these steps, you should be able to get your legacy web application running on your M1 Mac. Let me know if you'd like more details on any specific step!




Yes, it is safe to keep both ARM Homebrew and x86_64 Homebrew installed on the same system, and you can switch between them as needed. Since their installation paths are different (/opt/homebrew for ARM and /usr/local for Intel), they won't conflict as long as you manage their paths correctly.
Advantages of Keeping Both
1. Flexibility: You can work with both ARM-native tools and x86_64 tools without reinstallation.
2. Isolation: Each Homebrew instance will manage its dependencies separately, avoiding conflicts.

Managing Both Homebrew Installations
You can keep both versions installed and switch between them using the terminal. Here's how:
1. Use ARM Homebrew
Switch to ARM Homebrew for ARM-native tools:
arch -arm64 zsh
eval "$(/opt/homebrew/bin/brew shellenv)"
2. Use x86_64 Homebrew
Switch to Intel Homebrew for legacy tools:
arch -x86_64 zsh
eval "$(/usr/local/bin/brew shellenv)"
3. Permanent Setup (Optional)
You can modify your ~/.zshrc to make switching easier:
alias brew-arm="arch -arm64 /opt/homebrew/bin/brew"
alias brew-intel="arch -x86_64 /usr/local/bin/brew"
Then use:
* brew-arm install <package> for ARM
* brew-intel install <package> for x86_64

Recommendations
* Keep Both Homebrew Installations: This gives you the best of both worlds.
* Document Usage: Make a note of which architecture-specific tools or dependencies require each version of Homebrew.
* Test Your Workflow: Ensure your PATH environment variable is configured correctly for each shell session.
Let me know if you’d like detailed steps to configure your environment! 😊






Based on your detailed summary, you are attempting to set up a legacy app with Ruby 2.3.5, which is tightly bound to specific dependencies, including an older OpenSSL version, to ensure compatibility. Below, I’ll summarize your journey, explain the rationale for each step, and offer guidance for resolving your current issue.

Summary of Troubleshooting Journey
1. Initial Setup
* Goal: Install Ruby 2.3.5 for a legacy application.
* Environment: M1/M2 macOS system (ARM64 architecture).
* Issue: Ruby 2.3.5 compilation error due to incompatibility with the ARM64 architecture.
* Action Taken: Switched architecture to x86_64 using Rosetta, which allows older Ruby versions to compile.
2. OpenSSL Compatibility
* Problem: Ruby 2.3.5 has strict OpenSSL version requirements.
* First Attempt: Tried OpenSSL 1.1.1, which resolved compilation issues but caused runtime errors due to LoadError.
    * Rationale: OpenSSL 1.1.1 is more secure and widely used, but Ruby 2.3.5 was designed for earlier OpenSSL versions.
* Second Attempt: Downgraded to OpenSSL 1.0.0 to match Ruby 2.3.5's expected environment.
    * Outcome: Resolved the LoadError, but now encountering TLS protocol issues with curl.
3. TLS Protocol and Curl Error
* Error Details:
    * curl: (35) error:1407742E:SSL routines:SSL23_GET_SERVER_HELLO:tlsv1 alert protocol version
    * This indicates the server requires a more modern TLS protocol (e.g., TLS 1.2 or TLS 1.3), but OpenSSL 1.0.0 only supports outdated versions (TLS 1.0 and 1.1).
* Root Cause: The downgrade to OpenSSL 1.0.0 made Ruby compatible, but it broke modern TLS communication.

Key Considerations
1. Why OpenSSL 1.0.0?
    * Ruby 2.3.5 is old and expects features and behavior specific to OpenSSL 1.0.x. However, modern environments are incompatible with this version due to security deprecations.
2. Why the TLSv1 alert protocol error?
    * The Ruby source URL (https://ftp.ruby-lang.org) enforces TLS 1.2 or higher for secure downloads. OpenSSL 1.0.0 cannot negotiate this protocol version.
3. Where Are We Now?
    * You’re stuck in a Catch-22: Downgrading OpenSSL resolves Ruby compatibility but breaks network communications for critical operations like downloading source files.

Resolution
To move forward, you must either:
1. Work Around the TLS Issue (Recommended for Short-Term Setup)
    * Use a modern version of OpenSSL temporarily for downloading Ruby, and then switch back to OpenSSL 1.0.0 after Ruby is installed.
2. Create a Dockerized Legacy Environment (Recommended for Long-Term Maintenance)
    * Use a pre-configured container with Ruby 2.3.5 and OpenSSL 1.0.x to isolate dependencies.

Option 1: Work Around the TLS Issue
Here’s how to proceed step-by-step:
Step 1: Temporarily Use Modern OpenSSL
1. Switch to OpenSSL 3.x or 1.1.1 for downloading Ruby: export PATH="/usr/local/Cellar/openssl@3/3.4.0/bin:$PATH"
2. export LDFLAGS="-L/usr/local/Cellar/openssl@3/3.4.0/lib"
3. export CPPFLAGS="-I/usr/local/Cellar/openssl@3/3.4.0/include"
4. export PKG_CONFIG_PATH="/usr/local/Cellar/openssl@3/3.4.0/lib/pkgconfig"
5. 
6. Reattempt downloading Ruby: rvm reinstall 2.3.5 --with-openssl-dir=/usr/local/Cellar/openssl@3/3.4.0
7. 
Step 2: Switch Back to OpenSSL 1.0.0
1. After installation, switch the OpenSSL version back to 1.0.0 for Ruby runtime: rvm install 2.3.5 --with-openssl-dir=/usr/local/openssl-1.0.0
2. 
Step 3: Validate
* Confirm Ruby is installed: ruby -v
* 
* Verify OpenSSL compatibility: ruby -ropenssl -e 'puts OpenSSL::OPENSSL_VERSION'
* 

Option 2: Use Docker for Legacy Environment
This approach isolates dependencies and avoids polluting your system with incompatible versions:
1. Create a Dockerfile: FROM ubuntu:18.04
2. 
3. # Install dependencies
4. RUN apt-get update && apt-get install -y \
5.     curl \
6.     build-essential \
7.     libssl1.0-dev \
8.     libreadline-dev \
9.     zlib1g-dev
10. 
11. # Install RVM and Ruby 2.3.5
12. RUN curl -sSL https://get.rvm.io | bash -s stable
13. RUN /bin/bash -l -c "rvm install 2.3.5"
14. RUN /bin/bash -l -c "rvm use 2.3.5 --default"
15. 
16. # Set working directory
17. WORKDIR /app
18. 
19. Build and run the container: docker build -t legacy-app .
20. docker run -it -v $(pwd):/app legacy-app /bin/bash
21. 

Final Recommendations
1. For Immediate Setup: Use Option 1 to bypass TLS issues and complete the installation.
2. For Long-Term Maintenance: Transition to Docker or another containerized solution to handle legacy dependencies more cleanly.
3. Consider Updating: If feasible, start planning to upgrade the application to a more modern Ruby version to avoid similar issues in the future.



- - - - - - - — - - - - — - - - - - — - - - - - - - - — - - - - - - - — - - - - — - - - — - - —  — - - - - - — - - — - - — - - —  — — - - - - —   Your detailed recap captures the immense effort and technical depth you invested in making the legacy application work on an M1 Mac. Below, I've structured and refined your summary to ensure it reflects the full breadth of your challenges and solutions while emphasizing the innovative problem-solving and troubleshooting steps you undertook.
* 
* Recap of R&D/Analysis & Troubleshooting for Running Legacy Web Application on M1 Mac
* Objective
* To run a legacy web application on a Mac with an M1 chip, ensuring compatibility with:
* Ruby 2.2.2 / 2.3.3
* Rails 4.1.6
* MySQL 5.7.x (required for compatibility with mysql2 gem v0.3.18)
* 
* Key Challenges Encountered
* Architecture Compatibility Issues
    * The legacy application and its dependencies were designed for Intel (x86_64) architecture, while M1 Macs utilize ARM64 architecture.
    * Compilation failures occurred when building tools and libraries due to mismatched architectures.
* Dependency Compatibility
    * Ruby 2.3.3 is outdated and not fully compatible with modern systems.
    * The mysql2 gem version 0.3.18 requires MySQL 5.7.x and Ruby 2.3.3, creating a strict dependency chain.
    * Modern library versions (e.g., libffi) conflicted with requirements of older Ruby versions.
* Library Compatibility
    * libffi v3.4 caused build errors due to incompatibility with Ruby 2.3.3.
    * Attempts to downgrade to libffi v3.3 encountered issues with ARM64 compilation.
* Tooling and Environment
    * MySQL 5.7.x installation and configuration were challenging on M1.
    * Bundling gems and compiling Ruby dependencies failed due to mismatched architectures.
* 
* Troubleshooting Steps
* Exploration of Rosetta 2
    * Objective: Bypass ARM64-related issues by running tools in Intel (x86_64) emulation mode.
    * Steps Taken:
        * Configured the terminal to run in Rosetta mode using arch -x86_64.
        * Installed and compiled Ruby, MySQL, and gems in this mode.
        * Successfully avoided architecture conflicts with older dependencies.
* Manual Dependency Management
    * Installed libffi v3.3 manually for compatibility with Ruby.
    * Configured MySQL 5.7.x via Homebrew in Intel mode, ensuring proper linkage with the mysql2 gem.
* Rebuilding Ruby for ARM64 (Attempted Alternative)
    * Explored rebuilding Ruby 2.3.3 natively for ARM64 but encountered insurmountable compatibility issues due to outdated code.
* Environment Configuration
    * Set up environment variables in .zshrc to manage paths for Ruby, MySQL, and libraries: export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"
    * 
    * Configured the mysql2 gem to link with MySQL 5.7 using: bundle config build.mysql2 --with-mysql-config=/usr/local/opt/mysql@5.7/bin/mysql_config
    * 
* Testing and Validation
    * Validated the legacy application functionality using Rosetta 2 with all dependencies successfully installed and configured.
* 
* Final Solution
* Run the Legacy Application in Rosetta 2 (x86_64) Mode
    * Installed Ruby, MySQL, and other dependencies in Intel mode using Rosetta 2.
    * Successfully ran the application without requiring extensive rebuilding of outdated dependencies.
* Short-Term Workaround: Use Rosetta 2 for running the application while ensuring compatibility with legacy dependencies.
* 
* Future Considerations
* Gradual Modernization
    * Upgrade Ruby, Rails, and MySQL versions incrementally to reduce reliance on Rosetta 2.
    * Refactor the application codebase for compatibility with modern architectures and libraries.
* Dockerization
    * Use Docker to encapsulate the legacy environment, ensuring consistent performance and portability across platforms.
* M1 Native Setup
    * Investigate rebuilding critical dependencies (e.g., Ruby 2.3.3, MySQL) for ARM64 to eliminate Rosetta 2 reliance over time.
* 
* Key Takeaways
* Leveraging Rosetta 2 was a creative and effective solution to bridge architecture gaps for a legacy application.
* Detailed dependency management and configuration ensured compatibility without compromising application functionality.
* The approach balanced short-term compatibility with long-term modernization plans.
* Your meticulous R&D efforts and troubleshooting demonstrate excellent technical expertise and perseverance. This achievement showcases your ability to tackle complex legacy system challenges effectively while paving the way for future scalability and stability. 🚀

Gemfile -   source 'https://rubygems.org'


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

  

