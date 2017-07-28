---
layout: post
title: Build A Restful JSON Api With Rails 5
---

Today, we are going to disscuss about **building a Restful API solution with rails 5** to get all transactions for a specifc consumer and or for a merchant. There should be three endpoints one for each dataset / table.

As of version 5, Rails core now supports API only applications! In previous versions, we relied on an external `gem: rails-api` which has since been merged to core rails.

This works to generate an API-centric framework excluding functionality that would otherwise be unused and unnecessary.

Here is code for reference [CODE](https://github.com/rah00l/transactions-api)

### Prerequisites:

make sure you have ruby version >=2.2.2 and rails version 5.

### API Endpoints

**Consumers**

	consumers GET   /consumers(.:format)     consumers#index
		POST  /consumers(.:format)     consumers#create
		PUT   /consumers/:id(.:format) consumers#update
**Merchants**

	merchants GET   /merchants(.:format)     merchants#index
		POST  /merchants(.:format)     merchants#create
		PUT   /merchants/:id(.:format) merchants#update
**Transactions - (cunsumer & merchant related)**

	consumer_transactions GET   /consumers/:consumer_id/transactions(.:format)     transactions#index
		POST  /consumers/:consumer_id/transactions(.:format)     transactions#create
		PUT   /consumers/:consumer_id/transactions/:id(.:format) transactions#update

	merchant_transactions GET   /merchants/:merchant_id/transactions(.:format)     transactions#index
		POST  /marchants/:merchant_id/transactions(.:format)     transactions#create		
		PUT   /merchants/:merchant_id/transactions/:id(.:format) transactions#update

### Project Setup

Generate a new project `transactions-api` by running:
	
	rails new transactions-api --api -T

**Dependencies**

Let's take a moment to review the gems that we'll be using.

* [rspec-rails](https://github.com/rspec/rspec-rails) - Testing framework.
* [factory_girl_rails](https://github.com/thoughtbot/factory_girl_rails) - A fixtures replacement with a more straightforward syntax. You'll see.
* [shoulda_matchers](https://github.com/thoughtbot/shoulda-matchers) - Provides RSpec with additional matchers.
* [database_cleaner](https://github.com/DatabaseCleaner/database_cleaner) - You guessed it! It literally cleans our test database to ensure a clean state in each test suite.
* [faker](https://github.com/stympy/faker) - A library for generating fake data. We'll use this to generate test data.

Add `rspec-rails` to both the `:development` and `:test` groups.

{% highlight ruby %}
# Gemfile
group :development, :test do
  gem 'rspec-rails', '~> 3.5'
end
{% endhighlight %}

Add `factory_girl_rails`, `shoulda_matchers`, `faker` and `database_cleaner` to the :test group.

{% highlight ruby %}
# Gemfile
group :test do
 gem 'factory_girl_rails', '~> 4.0'
 gem 'shoulda-matchers', '~> 3.1'
 gem 'faker'
 gem 'database_cleaner'
end
{% endhighlight %}

Install the gems by running:

	$ bundle install

Initialize the spec directory (where our tests will reside).

	$ rails generate rspec:install

This adds the following files which are used for configuration:

* `.rspec`
* `spec/spec_helper.rb`
* `spec/rails_helper.rb`

Create a factories directory (factory girl uses this as the default directory). This is where we'll define the model factories.

	$ mkdir spec/factories

**Configuration**

In spec/rails_helper.rb

{% highlight ruby %}
# require database cleaner at the top level
require 'database_cleaner'

# [...]
# configure shoulda matchers to use rspec as the test framework and full matcher libraries for rails
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end

# [...]
RSpec.configuration do |config|
  # [...]
  # add `FactoryGirl` methods
  config.include FactoryGirl::Syntax::Methods

  # start by truncating all the tables but then use the faster transaction strategy the rest of the time.
  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
    DatabaseCleaner.strategy = :transaction
  end

  # start the transaction strategy as examples are run
  config.around(:each) do |example|
    DatabaseCleaner.cleaning do
      example.run
    end
  end
  # [...]
end
{% endhighlight %}

**Model**

	rails g model merchant name domain

The generator invokes active record and rspec to generate the migration, model, and spec respectively.

{% highlight ruby %}
class CreateMerchants < ActiveRecord::Migration[5.1]
  def change
    create_table :merchants do |t|
      t.string :name
      t.string :domain

      t.timestamps
    end
  end
end
{% endhighlight %}

	rails g model consumer first_name last_name

{% highlight ruby %}
class CreateConsumers < ActiveRecord::Migration[5.1]
  def change
    create_table :consumers do |t|
      t.string :first_name
      t.string :last_name
      t.timestamps
    end
  end
end
{% endhighlight %}

	rails g model transaction consumer:references merchant:references sale_amount:decimal date:date

{% highlight ruby %}
class CreateTransactions < ActiveRecord::Migration[5.1]
  def change
    create_table :transactions do |t|
      t.references :consumer, foreign_key: true
      t.references :merchant, foreign_key: true
      t.decimal :sale_amount
      t.date :date
      t.timestamps
    end
  end
end
{% endhighlight %}

By adding `consumer:references` and `merchant:references` we're telling the generator to set up an association with the Transact model. This will do the following:

* Add a foreign key column `consumer_id` and `merchant_id` to the transactions table
* Setup a `belongs_to` association in the Transaction model

Let's run the migrations.

	$ rails db:migrate

let's write the model specs first.

**consumer**

{% highlight ruby %}
# spec/models/consumer.rb
require 'rails_helper'

RSpec.describe Consumer, type: :model do
  # Test association
  # ensure Consumer model has a m:m relationship with the Merchant model through transactions

  it { should have_many(:transactions) }
  it { should have_many(:merchants).dependent(:destroy) }
end
{% endhighlight %}

**merchant**

{% highlight ruby %}
# spec/models/merchant.rb
require 'rails_helper'

RSpec.describe Merchant, type: :model do
  # Test association
  # ensure Merchant model has a m:m relationship with the Consumer model through transactions
  it { should have_many(:transactions) }
  it { should have_many(:consumers).dependent(:destroy) }
end
{% endhighlight %}

**transaction**

{% highlight ruby %}
# spec/models/transaction.rb
require 'rails_helper'

RSpec.describe Transaction, type: :model do
  it { should belong_to(:consumer) }
  it { should belong_to(:merchant) }
end
{% endhighlight %}

Run the test suite for models,

	rahul@rahul-Inspiron-N5010 transactions-api (master) $rspec spec/models
	FFFFFF

	Failures:

	  1) Consumer should have many transactions
	     Failure/Error: it { should have_many(:transactions) }
	       Expected Consumer to have a has_many association called transactions (no association called transactions)
	     # ./spec/models/consumer_spec.rb:6:in `block (2 levels) in <top (required)>'
					database_cleaner/configuration.rb:87:in `cleaning'
	     # ./spec/rails_helper.rb:55:in `block (2 levels) in <top (required)>'

	  2) Consumer should have many merchants dependent => destroy
	     Failure/Error: it { should have_many(:merchants).dependent(:destroy) }
	       Expected Consumer to have a has_many association called merchants (no association called merchants)

	 ....................................
	       
	Finished in 0.78579 seconds (files took 1.73 seconds to load)
	6 examples, 6 failures

	Failed examples:

	rspec ./spec/models/consumer_spec.rb:6 # Consumer should have many transactions
	rspec ./spec/models/consumer_spec.rb:7 # Consumer should have many merchants dependent => destroy
	rspec ./spec/models/merchant_spec.rb:6 # Merchant should have many transactions
	rspec ./spec/models/merchant_spec.rb:7 # Merchant should have many consumers dependent => destroy
	rspec ./spec/models/transaction_spec.rb:4 # Transaction should belong to consumer
	rspec ./spec/models/transaction_spec.rb:5 # Transaction should belong to merchant


Let's go ahead and fix the failures.

{% highlight ruby %}
class Consumer < ApplicationRecord
  # Associations
  has_many :transactions
  has_many :merchants, through: :transactions, dependent: :destroy
end

class Merchant < ApplicationRecord
  # Associations
  has_many :transactions
  has_many :consumers, through: :transactions, dependent: :destroy
end

class Transaction < ApplicationRecord
  belongs_to :consumer
  belongs_to :merchant
end
{% endhighlight %}

Run the tests again

<img src="{{ site.url }}/public/restfull_api_testsuit_ran1.png" />

All Green!

#### Controller

let's generate the controllers.

	rails g controller consumers index create update
	rails g controller merchants index create update
	rails g controller transactions index create update

we won't be writing any controller specs. We're going to write request specs instead.

Request specs are designed to drive behavior through the full stack, including routing. 

>The official recommendation of the Rails team and the RSpec core team is to write request specs instead.	

Add a requests folder to the `spec` directory with the corresponding spec files.

	mkdir spec/requests && touch spec/requests/consumers_spec.rb touch spec/requests/merchants_spec.rb touch spec/requests/transactions_spec.rb

Before we define the request specs, Let's add the model factories which will provide the test data.

	touch spec/factories/consumer.rb touch spec/factories/merchant.rb touch spec/factories/transaction.rb

**Define the factories.**

{% highlight ruby %}	
# spec/factories/consumer.rb
FactoryGirl.define do
  factory :consumer do
    first_name { Faker::Lorem.word }
    last_name { Faker::Lorem.word }
  end
end
{% endhighlight %}

{% highlight ruby %}
# spec/factories/merchant.rb
FactoryGirl.define do
  factory :merchant do
    name { Faker::Lorem.word }
    domain { Faker::Lorem.word }
  end
end
{% endhighlight %}


{% highlight ruby %}
# spec/factories/transaction.rb
FactoryGirl.define do
  factory :transaction do
    sale_amount { Faker::Number.number(2) }
    date { Faker::Number.number(10) }
  end
end
{% endhighlight %}

By wrapping faker methods in a block, we ensure that faker generates dynamic data every time the factory is invoked.

**Consumer API**

{% highlight ruby %}
require 'rails_helper'

RSpec.describe 'Consumer', type: :request do
  # initialize test data
  let!(:consumer) { create_list(:consumer, 10) }
  let(:id) { consumer.first.id }

  # Test suite for GET /consumers
  describe 'GET /consumers' do
    before { get '/consumers' }

    it 'returns consumers' do
      # Note `json` is a custom helper to parse JSON responses
      expect(json).not_to be_empty
      expect(json.size).to eq(10)
    end

    it 'returns status code 200' do
      expect(response).to have_http_status(200)
    end
  end

  describe 'POST /consumers' do
    let(:valid_attributes) { { first_name: 'Learn', last_name: 'Lashdsd' } }

    context 'when request is valid' do
      before { post '/consumers', params: valid_attributes }

      it 'creates a consumer' do
        expect(json['first_name']).to eq('Learn')
      end

      it 'returns status code 201' do
        expect(response).to have_http_status(201)
      end
    end
  end

  describe 'PUT /consumers/:id' do
    let(:valid_attributes) { { first_name: 'Shopping' } }

    before { put "/consumers/#{id}", params: valid_attributes }

    context 'when the record exists' do
      it 'updates the record' do
        expect(response.body).to be_empty
      end

      it 'updates the record with first name field' do
        updated_consumer = Consumer.find(id)
        expect(updated_consumer.first_name).to eq('Shopping')
      end

      it 'returns status code 204' do
        expect(response).to have_http_status(204)
      end
    end

    context 'when consumer does not exist' do
      let(:id) { 0 }

      it 'returns status code 404' do
        expect(response).to have_http_status(404)
      end

      it 'returns a not found message' do
        expect(response.body).to match(/Couldn't find Consumer/)
      end
    end
  end
end
{% endhighlight %}

We also have a custom helper method `json` which parses the JSON response to a Ruby Hash which is easier to work with in our tests. 

Let's define it in `spec/support/request_spec_helper`.

Add the directory and file:

	$ mkdir spec/support && touch spec/support/request_spec_helper.rb

{% highlight ruby %}
# spec/support/request_spec_helper.rb
module RequestSpecHelper
  def json
    JSON.parse(response.body)
  end
end
{% endhighlight %}

The support directory is not autoloaded by default. To enable this, open the rails helper and comment out the support directory auto-loading and then include it as shared module for all request specs in the RSpec configuration block.

{% highlight ruby %}
# spec/rails_helper.rb
# [...]
Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }
# [...]
RSpec.configuration do |config|
  # [...]
  config.include RequestSpecHelper, type: :request
  # [...]
end
{% endhighlight %}

Run the tests.

	rahul@rahul-Inspiron-N5010 transactions-api (master) $rspec spec
	......FFFFFFFFF

	Failures:

	  1) Consumer GET /consumers returns consumers
	     Failure/Error: before { get '/consumers' }
	     
	     LoadError:
	       Unable to autoload constant ConsumersController, expected /home/rahul/workspace/transactions-api/app/controllers/consumers_controller.rb to define it
	     # /home/rahul/.rvm/gems/ruby-2.3.3@transactions-api/gems/rack-2.0.3/lib/rack/etag.rb:25:in `call'
	     # /home/rahul/.rvm/gems/ruby-2.3.3@transactions-api/gems/rack-2.0.3/lib/rack/conditional_get.rb:25:in `call'
	     # /home/rahul/.rvm/gems/ruby-2.3.3@transactions-api/gems/rack-2.0.3/lib/rack/head.rb:12:in `call'

We get failing routing errors. This is because we haven't defined the routes yet.

Go ahead and define them in `config/routes.rb`.

{% highlight ruby %}
	Rails.application.routes.draw do
	  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

	  resources :consumers, only: [:index, :create, :update]
	end
{% endhighlight %}

When we run the tests we see that the routing error is gone. As expected we have controller failures. Let's go ahead and define the controller methods.

{% highlight ruby %}
class ConsumersController < ApplicationController
    before_action :set_consumer, only: [:update]
  # GET /consumers
  def index
    @consumers = Consumer.all
    json_response(@consumers)
  end

  # POST /consumers
  def create
    @consumer = Consumer.create!(consumer_params)
    json_response(@consumer, :created)
  end

  # PUT /consumers/:id
  def update
    @consumer.update(consumer_params)
    head :no_content
  end

  private

  def consumer_params
  # whitelist params
    params.permit(:first_name, :last_name)
  end

  def set_consumer
    @consumer = Consumer.find(params[:id])
  end
end
{% endhighlight %}

More helpers.
	
* `json_response` which does... yes, responds with JSON and an HTTP status code (200 by default). We can define this method in concerns folder.

{% highlight ruby %}
# app/controllers/concerns/response.rb

module Response
  def json_response(object, status = :ok)
    render json: object, status: status
  end
end
{% endhighlight %}

* `set_consumer` - callback method to find a consumer by id. In the case where the record does not exist, ActiveRecord will throw an exception `ActiveRecord::RecordNotFound`. We'll rescue from this exception and return a 404 message.

{% highlight ruby %}
module ExceptionHandler
  extend ActiveSupport::Concern

  included do
    rescue_from ActiveRecord::RecordInvalid, with: :four_twenty_two

    rescue_from ActiveRecord::RecordNotFound do |e|
      json_response({ message: e.message }, :not_found)
    end
  end

  private

  def four_twenty_two(e)
    json_response({ message: e.message }, :unprocessable_entity)
  end
end
{% endhighlight %}

In our `create` method in the `CounsumersController`, note that we're using `create!` instead of `create`. This way, the model will raise an exception `ActiveRecord::RecordInvalid`. This way, we can avoid deep nested if statements in the controller. Thus, we rescue from this exception in the `ExceptionHandler` module.

However, our controller classes don't know about these helpers yet. Let's fix that by including these modules in the application controller.

{% highlight ruby %}
class ApplicationController < ActionController::API
  include Response
  include ExceptionHandler
end
{% endhighlight %}

Run the tests and everything's all green!

<img src="{{ site.url }}/public/restfull_api_testsuit_ran2.png" />

Let's have some manual testing.

	rails s

Let's go ahead and make requests to the API. I'll be using httpie as my HTTP client.

	POST /consumers(.:format)  consumers#create
	http POST :3000/consumers first_name=Aarmbh last_name=Pal

<img src="{{ site.url }}/public/consumers-2.png" />

	consumers GET /consumers(.:format)		consumers#index
	http :3000/consumers

	PUT /consumers/:id(.:format) consumers#update
	http PUT :3000/consumers/3 first_name=Ananya

<img src="{{ site.url }}/public/consumers-1.png" />

**Merchant API**

{% highlight ruby %}

require 'rails_helper'

RSpec.describe 'Merchant', type: :request do
  # initialize test data
  let!(:merchant) { create_list(:merchant, 10) }
  let(:id) { merchant.first.id }

  # Test suite for GET /merchants
  describe 'GET /merchants' do
    before { get '/merchants' }

    it 'returns merchants' do
      # Note `json` is a custom helper to parse JSON responses
      expect(json).not_to be_empty
      expect(json.size).to eq(10)
    end

    it 'returns status code 200' do
      expect(response).to have_http_status(200)
    end
  end

  describe 'POST /merchants' do
    let(:valid_attributes) { { name: 'Learn', domain: 'Lashdsd' } }

    context 'when request is valid' do
      before { post '/merchants', params: valid_attributes }

      it 'creates a merchant' do
        expect(json['name']).to eq('Learn')
      end

      it 'returns status code 201' do
        expect(response).to have_http_status(201)
      end
    end
  end

  describe 'PUT /merchants/:id' do
    let(:valid_attributes) { { name: 'Shopping' } }

    before { put "/merchants/#{id}", params: valid_attributes }

    context 'when the record exists' do
      it 'updates the record' do
        expect(response.body).to be_empty
      end

      it 'updates the record with first name field' do
        updated_merchant = Merchant.find(id)
        expect(updated_merchant.name).to eq('Shopping')
      end

      it 'returns status code 204' do
        expect(response).to have_http_status(204)
      end
    end

    context 'when merchant does not exist' do
      let(:id) { 0 }

      it 'returns status code 404' do
        expect(response).to have_http_status(404)
      end

      it 'returns a not found message' do
        expect(response.body).to match(/Couldn't find Merchant/)
      end
    end
  end
end

{% endhighlight %}

As expected, running the tests at this point should output failing merchant tests. Let's define the merchants controller.

{% highlight ruby %}

class MerchantsController < ApplicationController
  before_action :set_merchant, only: [:update]
  # GET /merchants
  def index
    @merchants = Merchant.all
    json_response(@merchants)
  end

  # POST /merchants
  def create
    @merchant = Merchant.create!(merchant_params)
    json_response(@merchant, :created)
  end

  # PUT /merchants/:id
  def update
    @merchant.update(merchant_params)
    head :no_content
  end

  private

  def merchant_params
    # whitelist params
      params.permit(:name, :domain)
  end

  def set_merchant
    @merchant = Merchant.find(params[:id])
  end
end

{% endhighlight %}

Go ahead and re-define them in `config/routes.rb`.

{% highlight ruby %}
	Rails.application.routes.draw do
	  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

	  resources :consumers, only: [:index, :create, :update]
	  resources :merchants, only: [:index, :create, :update]
	end
{% endhighlight %}

<img src="{{ site.url }}/public/restfull_api_testsuit_ran3.png" />

Run some manual tests for the `merchants API`:

	merchants GET /merchants(.:format)	merchants#index
	http :3000/merchants

<img src="{{ site.url }}/public/merchant-1.png" />

	POST /merchants(.:format)		merchants#create
	http POST :3000/merchants name=merchant_name domain=domain_name

<img src="{{ site.url }}/public/merchant-2.png" />

	PUT /merchants/:id(.:format) merchants#update
	http PUT :3000/merchants/4 name="PVR Cinemas"

<img src="{{ site.url }}/public/merchant-3.png" />

**Transactions API**

{% highlight ruby %}

require 'rails_helper'

RSpec.describe 'Transactions API' do
  let!(:consumer) { create(:consumer) }
  let!(:merchant) { create(:merchant) }

  let(:consumer_id) { consumer.id }
  let(:merchant_id) { merchant.id }

  let!(:transaction) { create(:transaction, consumer_id: consumer.id, merchant_id: merchant.id) }
  let(:id) { transaction.id }

# GET all transactions for a specific consumer
  describe 'GET /consumers/:consumer_id/transactions' do
    before { get "/consumers/#{consumer_id}/transactions" }

    context 'when transactions exists' do
      it 'returns status code 200' do
        expect(response).to have_http_status(200)
      end

      it 'returns all transactions for a specific consumer' do
        expect(json.size).to eq(1)
      end
    end

    context 'when transactions does not exist for a given merchant' do
      let(:consumer_id) { 0 }

      it 'returns status code 404' do
        expect(response).to have_http_status(404)
      end

      it 'returns a not found message' do
        expect(response.body).to match(/Couldn't find Consumer/)
      end
    end
  end

# GET all transactions for a specific merchant
  describe 'GET /merchants/:merchant_id/transactions' do
    before { get "/merchants/#{merchant_id}/transactions" }

    context 'when transactions exists' do
      it 'returns status code 200' do
        expect(response).to have_http_status(200)
      end

      it 'returns all transactions for a specific merchant' do
        expect(json.size).to eq(1)
      end
    end

    context 'when transactions does not exist for a given merchant' do
      let(:merchant_id) { 0 }

      it 'returns status code 404' do
        expect(response).to have_http_status(404)
      end

      it 'returns a not found message' do
        expect(response.body).to match(/Couldn't find Merchant/)
      end
    end
  end

# POST a transaction to generate transaction
  describe 'POST /consumers/:consumer_id/transactions' do
    let(:valid_attributes) { { merchant_id: merchant_id, sale_amount: '112.34', date: '1' } }

    context 'when request is valid' do
      before { post "/consumers/#{consumer_id}/transactions", params: valid_attributes }

      it 'creates a transaction' do
        expect(json['sale_amount']).to eq('112.34')
      end

      it 'returns status code 201' do
        expect(response).to have_http_status(201)
      end
    end
  end

  describe 'PUT /consumers/:consumer_id/transactions/:id' do
    let(:valid_attributes) { { sale_amount: '201.99' } }

    before { put "/consumers/#{consumer_id}/transactions/#{id}", params: valid_attributes }

    context 'when transaction exists' do
      it 'returns status code 204' do
        expect(response).to have_http_status(204)
      end

      it 'updates the transaction' do
        updated_transaction = Transaction.find(id)
        expect(updated_transaction.sale_amount.to_s).to eq('201.99')
      end
    end

    context 'when transaction does not exist' do
      let(:id) { 0 }

      it 'returns status code 404' do
        expect(response).to have_http_status(404)
      end

      it 'returns a not found message' do
        expect(response.body).to match(/Couldn't find Transaction/)
      end
    end
  end
end

{% endhighlight %}

Running the tests should output failing transaction tests. Let's define the transactions controller.

{% highlight ruby %}

class TransactionsController < ApplicationController
  before_action :get_transactable, only: [:index]
  before_action :set_transaction, only: [:update]
  # GET /consumers/:consumer_id/transactions
  # GET /merchants/:merchant_id/transactions
  def index
    json_response(@transactable.transactions)
  end

  # POST /consumers/:consumer_id/transactions
  # POST /merchants/:merchant_id/transactions
  def create
    @transaction = Transaction.create!(transaction_params)
    json_response(@transaction, :created)
  end

  # PUT /consumers/:consumer_id/transactions/:id
  # PUT /merchants/:merchant_id/transactions/:id
  def update
    @transaction.update(transaction_params)
    head :no_content
  end

  private

  def get_transactable
    @transactable = if params[:consumer_id].present?
      Consumer.find(params[:consumer_id])
    else
      Merchant.find(params[:merchant_id])
    end
  end

  def transaction_params
    params.permit(:consumer_id, :merchant_id, :sale_amount, :date)
  end

  def set_transaction
    @transaction = Transaction.find(params[:id])
  end
end

{% endhighlight %}

Go ahead and re-define routes in `config/routes.rb`.

{% highlight ruby %}

Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  concern :transactionable do
    resources :transactions, only: [:index, :create, :update]
  end

  resources :consumers, only: [:index, :create, :update], concerns: :transactionable
  resources :merchants, only: [:index, :create, :update], concerns: :transactionable
end

{% endhighlight %}



<img src="{{ site.url }}/public/restfull_api_testsuit_ran4.png" />

Run some manual tests for the `transactions API`:

	consumer_transactions GET /consumers/:consumer_id/transactions(.:format)  transactions#index
	http :3000/consumers/1/transactions

<img src="{{ site.url }}/public/transact1.png" />

	merchant_transactions GET /merchants/:merchant_id/transactions(.:format)  transactions#index
	http :3000/merchants/1/transactions

<img src="{{ site.url }}/public/transact2.png" />

	POST /consumers/:consumer_id/transactions(.:format)		transactions#create
	http POST :300/consumers/1/transactions merchant_id=1 sale_amount=111 date=2017/04/11

	POST /marchants/:merchant_id/transactions(.:format)		transactions#create
	http POST :3000/merchants/1/transactions consumer_id=1 sale_amount=222	date=2017/04/22

<img src="{{ site.url }}/public/transact3.png" />

	PUT /consumers/:consumer_id/transactions/:id(.:format) transactions#update
	http PUT :3000/consumers/1/transactions/1 sale_amount=444

<img src="{{ site.url }}/public/transact4.png" />

	PUT /merchants/:merchant_id/transactions/:id(.:format) transactions#update
	http PUT :3000/merchants/1/transactions/1 sale_amount=555

<img src="{{ site.url }}/public/transact5.png" />

	http POST /marchants/:merchant_id/transactions with invalid merchant id 100

<img src="{{ site.url }}/public/transact6.png" />

***

Here we have covered following points:

* Generate an API application with `Rails 5`.
* Setup `RSpec` testing framework with `Factory Girl`, `Database Cleaner`, `Shoulda Matchers` and `Faker`.
* Build models and controllers with `TDD (Test Driven Development)`.
* Make HTTP requests to an API with `httpie`.

*Thanks for reading!*