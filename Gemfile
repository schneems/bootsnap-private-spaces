source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '3.2.1'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
# gem 'rails', '~> 6.1.7.1'
gem 'rails', '~> 7.0', '>= 7.0.4.2'
# Use mysql2 as the database for Active Record
gem 'mysql2','~> 0.5.3'
#use elasticsearch adapter to connect to elasticsearch
gem 'elasticsearch', '~> 7.17', '>= 7.17.7'
#use hiredis and redis to connect to redis
gem 'hiredis', '~> 0.6.3'
gem 'redis', '~> 5.0.6'
# gem 'redis', '~> 4.1', '>= 4.1.3', :require => ["redis", "redis/connection/hiredis"]
# Use Puma as the app server
gem 'puma', '~> 4.3', '>= 4.3.1'
#oj for optimized json dump and parsing
gem 'oj', '~> 3.14.2'

# gem 'devise', '~> 4.7.1'
gem 'doorkeeper', '~> 5.2.3'

gem 'money-open-exchange-rates'
gem 'money-rails','~> 1.15.0'

gem 'airbrake'
gem 'newrelic_rpm'

# Reduces boot times through caching; required in config/boot.rb
gem 'bootsnap', '>= 1.4.2', require: false

# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS), making cross-origin AJAX possible
gem 'rack-cors','~> 1.1.1'

group :development, :uat, :staging, :qa do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  gem 'pry'
  gem 'dotenv-rails'
end

group :development do
  gem 'brakeman'
  gem 'listen', '3.8.0'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
  gem 'rubocop', '~> 0.77.0', require: false
end

#use dry framework for request validation
gem 'dry-types', '1.7.0'
gem 'dry-struct', '1.6.0'
gem 'dry-validation', '1.10.0'


gem 'globalize', '~> 6.1.0'
gem 'carrierwave','~> 2.0.2' #TODO:: remove this dependency in next release
gem 'libxml-ruby', '~> 3.1'
gem 'rack-attack'
gem 'secure_headers'

# ruby async
gem 'async', '~> 1.2'
gem 'async-http'
gem 'concurrent-ruby', require: 'concurrent'
gem 'concurrent-ruby-edge', require: 'concurrent-edge'

# Elastic Search
gem 'elasticsearch-model', '~> 7.2.1'

# Sidekiq
gem 'sidekiq', '< 7'
gem 'redis-namespace', '1.10.0'
gem 'elastic-transport','8.1.0'

gem 'addressable', '~> 2.8.1'
