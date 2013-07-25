# Guard with Spork for RSpec, Cucumber and Test::Unit

by [JanDintel](https://github.com/jandintel) and [JobV](https://github.com/JobV)

## Gems
```ruby
group :development, :test
  gem 'guard-rspec'
  gem 'guard-livereload'
  gem 'spork-rails', github: 'sporkrb/spork-rails' # rubygems version not rails 4 compatible
  gem 'guard-spork'
  gem 'childprocess'
end

group :test do
  gem 'selenium-webdriver', '2.0.0'
  gem 'capybara', '2.1.0'
  gem 'factory_girl_rails'
  gem 'cucumber', '1.2.5' # Spork not supported as of Cucumber 1.3.0, need to use 1.2.5
  gem 'cucumber-rails', :require => false
  gem 'database_cleaner'
end
```

## Install instruction
```bash
$ bundle update
$ bundle install
$ rails generate rspec:install
$ guard init rspec
```
Modify ./Guardfile
```ruby
guard 'rspec', after_all_pass: false, cli: '--drb' do
...
```
Set up spork
```bash
$ bundle spork --bootstrap
```
Set up Spork in /spec/spec_helper.rb
```ruby
Spork.prefork do
  ENV["RAILS_ENV"] ||= 'test'
  require File.expand_path("../../config/environment", __FILE__)
  require 'rspec/rails'
  require 'rspec/autorun'
end
```
Setup guard with spork
```bash
$ guard init spork
```
Install Cucumber
```bash
$ rails generate cucumber:install --spork
```

## Mongoid

For usage with mongoid, modify /spec/spec_helper.rb.
Remove these lines:
```ruby
ActiveRecord::Migration.check_pending! if defined?(ActiveRecord::Migration)
..
config.fixture_path = "#{::Rails.root}/spec/fixtures"
..
config.use_transactional_fixtures = true
```
    
And add to /spec/spec_helper.rb, below Rspec.configure
```ruby
config.before(:suite) do
  DatabaseCleaner[:mongoid].strategy = :truncation
end

config.before(:each) do
  DatabaseCleaner[:mongoid].start
end

config.after(:each) do
  DatabaseCleaner[:mongoid].clean
end
```
