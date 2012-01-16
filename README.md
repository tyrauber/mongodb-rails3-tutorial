# MongoDB, Rails 3 Sample Application and Tutorial

The purpose of this tutorial is to provide step-by-step instruction on how to build a Rails 3 Application using MongoDB and MongoMapper. 

MongoDB is a NoSQL database that can be used as a replacement for SqlLite, MySQL, Postgresql or other sql databases.  NoSQL is a great alternative for web applications where scalability is required or data must be grouped together in greater numbers than the hard limits applied by sql-based databases.

Basic Ruby and Rails 3 knowledge will be required to follow this tutorial.

*Note:*  Everything *after* the dollar symbol ($) is intended to be terminal command.  Everything *after* a pound symbol (#) is intended to be a comment and should not be included in the terminal command.

## Prerequisites:

You must have the following installed on your system.

*  Ruby 1.9.2
*  Rails 3.1
*  MongoDB 2.0.2
*  Git

## Download the Sample Application

The complete application can be downloaded using the following terminal command:

	$ git clone git@github.com:tyrauber/mongodb-rails3-tutorial.git

To run the Rails application, type the following at the command prompt:

	$ cd mongodb-rails3-tutorial/

To run a Rails 3 application, this, type the following:

	$ rails server

# The Tutorial

## Part 1:  Basic MongoDB Rails 3 Application Creation

###  Step 1: Build a rails app using the  --skip-active-record option

The very first thing we are going to need to do is build the Rails 3 Application framework. Since MongoDB isn't a SQL database, it does not use ActiveRecord to store data. We will instead be using the MongoMapper gem as a replacement for ActiveRecord.  Therefore, we will need to '--skip-active-record' when generating our Rails 3 Application.

	$ rails new mongodb-rails3-tutorial --skip-active-record

Go ahead and change to the application directory:

	$ cd mongodb-rails3-tutorial/

### Step 2: Modify the Gemfile

Now that we have generated our Rails 3 application framework, we should modify our Gemfile to include the necessary gems.

To do so, we need to open GEMFILE in your favorite text editor. I prefer using nano from the command line:

	$ nano GEMFILE

At the top of the GEMFILE, you should see the following:

	source 'http://rubygems.org'

After that line, add the following:

	source 'http://gemcutter.org'

This expands the places where bundler (the tool used to install gems) may look to find the required gems, to include gemcutter.org in addition to rubygems.org.

We will now want to require both 'rubygems' and 'mongo' by adding the following under "source 'http://gemcutter.org:"

	require 'rubygems'
	require 'mongo'

We will then want to include the following gems:

	gem 'rails', '3.1.1'
	gem 'mongo_mapper'
	gem 'rest-client'
	gem 'hpricot'

In total, the top of our Gemfile should look something like this:

	source 'http://rubygems.org'
	source 'http://gemcutter.org'

	require 'rubygems'
	require 'mongo'

	gem 'rails', '3.1.1'
	gem 'mongo_mapper'
	gem 'rest-client'
	gem 'hpricot'

If so, save and exit the GEMFILE. In nano, type CONTROL-x, y, ENTER.

### Step 3:  Run the bundle installer:

Now let's install the required gems. To do so, type at the command prompt:

	$ bundle install

If successful, you should see a list of gem names with 'Using' in front of them. At the end of the list, it should say "Your bundle is complete!"

If for some reason this process fails, you may install a gem manually - for example 'mongodb' - through the following terminal command:

	$ gem install mongodb

You can rerun  'bundle install' to confirm successful gem installation.  

If you are unfamiliar with gems, the following commands are also handy:

	*	$ gem install name-of-gem		#Installs a gem
	*	$ gem uninstall name-of-gem 		#Uninstalls a gem
	*	$ gem list 				# Lists all gems installed in your system

### Step 4: Install the C Extensions for optimum MongoDB Ruby driver performance.

To improve MongoDB performance, we should install the C Extensions like so:

	$ gem install bson_ext

### Step 6:  Create a MongoDB initializer

We now need to add a MongoDB initializer by writing to 'config/initializers/mongo.rb'. At the command prompt:

	$  nano config/initializers/mongo.rb

Add the following code block:

	MongoMapper.connection = Mongo::Connection.new('localhost', 27017)
	MongoMapper.database = "#myapp-#{Rails.env}"

	if defined?(PhusionPassenger)
   		PhusionPassenger.on_event(:starting_worker_process) do |forked|
     			MongoMapper.connection.connect if forked
   		end
	end

When our rails app starts, the above code makes a connection to the Mongo database, sets the database name and makes the database connection accessible to PhusionPassenger, if applicable.

Save and exit the mongo.rb.

### Step 7: Create a MongoDB Rake Task

We should also add a MongoDB Rake task to make a tests be MongoDB aware. At the command prompt:

	$ lib/tasks/mongo.rake

Add the following code:

	namespace :db do
  		namespace :test do
    			task :prepare do
      				# Stub out for MongoDB
    			end
  		end
	end

Save and exit mongo.rake.

### Step 8: Run the server

In Rails 3, a rails server is started by typing:

	$ rails server

If all the requirements and gems are successfully installed, you should see the following:

	=> Booting WEBrick
	=> Rails 3.1.1 application starting in development on http://0.0.0.0:3000
	=> Call with -d to detach
	=> Ctrl-C to shutdown server
	[2012-01-12 17:31:06] INFO  WEBrick 1.3.1
	[2012-01-12 17:31:06] INFO  ruby 1.9.2 (2011-07-09) [x86_64-darwin11.1.0]
	[2012-01-12 17:31:06] INFO  WEBrick::HTTPServer#start: pid=34046 port=3000

If you see this, open a browser and navigate to: http://localhost:3000/

You should see the standard Rails Welcome Aboard page.

To kill the server, type CONTROL-c at the command prompt.

## Part 2:  Adding Functionality to a MongoDB, Rails 3 Application

This part of the tutorial is written to explain MongoDB specific changes to the Rails 3 development process.  The sample commands and code provided is for educational purposes and will not corrospond to files in the sample applicaion.

### No Migration

There are no migrations in a MongoDB Rails Application. MongoDB does not use ActiveRecord, therfore it doesn't require database migrations. The application data schema is structured in the model.

### Create a Model without ActiveRecord::Base

A MongoDB, Rails 3 application does not use ActiveRecord, therefore your model should not include "< ActiveRecord::Base" in your Model's class declartion.

For example, let's say we were creating a model called Movie, at app/models/movie.rb.

The top line of the model would simply be:

	class Entities

As opposed to the normal class declaration of:

	class Entities < ActiveRecord::Base

If you include ActiveRecord::Base, you will get errors when running the Rails Application because ActiveRecord is not included in the Application.

### Include MongoMapper:Document in the Model

We have required the MongoMapper gem in the application through the GEMFILE, but we must also include it in our models. Therefore, after our class declaration we must add the following line to our model:

	include MongoMapper::Document

### Key-Based Data Schema

In MongoDB, data is stored in json-formatted document storage as opposed to a relational database. Because there isn't ActiveRecord or Migrations, we use MongoMapper to define our data schema in the model directly.  To do so, we make key declarations, like so:

	key :title, String

This would mean that in the Movie Model, there is json keypair with the key 'title' and a string value.

MongoDB accepts the following key types:

	*	String
	*	Integer
	*	Time
	* 	Boolean
	* 	Array

To add timestamps to a model:

	timestamps!

### Validate Presence

You may validate the presence of a value, through:

	validates_presence_of :key_name

Or through the key declaration:

	key :key_name, key_type, :required => true

In our Movie Model example, we may require a title with the following key declaration:

	key :title, String, :required => true

### Validate Uniqueness

You may validate the uniqueness of a value, by:

	validates_uniqueness_of :key_name

Or through the key declaration:

	key :key_name, key_type, :unique => true

In our Movie Model example:

	key :title, String, :required => true, :unique => true

This validates the uniqueness of value, but does not actually create a unique key. To create a unique key:

	ensure_index :title, :unique => true 

### Validate Numericality

Similiarily, you may validate numericality of a value, by:

	validates_numericality_of :key_name

Or, in the key declaration:

	key :key_name, :numeric=> true

### Model Associations

In a relational database like MySQL, you may have One-to-Many or Many-to-Many assocaitions, through the use of 'belongs_to', 'many' and 'one' declarations. In a relational database, you could query these associations through a join. MongoDB doesn't have joins.

In MongoDB, you store an array of ids as a key value to respresent the association.

For example, let's say we have a Movie model and an Actor model. An actor belongs to a movie and a movie has many actors.

We would create the following models:

	class Movie
		include MongoMapper::Document
		key :title, 		String
		key :actor_ids, 	Array
		many :actors, :in => :actor_ids
	end

	class Actor
  		include MongoMapper::Document
		key :name, 	String
  		belongs_to :movie
	end

### Embedded Documents

MongoDB enables you to store documents within documents, as opposed to having each model be it's own collection of documents. This is useful when a document only belongs to one parent.

For example,


        class Movie
                include MongoMapper::Document
		key :title, String
		many :votes
        end

        class Vote
                include MongoMapper::EmbeddedDocument
                key :user_name,      	String
		key :rating,		Integer
        end









MongoDB doesn't have joins,

An Example Movie Model with MongoMapper would be as follows:

	class Movie
  		include MongoMapper::Document

		key :title,  		String
		key :description,   	String
		key :image,		String
  		key :minutes,         	Integer
  		key :release_date,     	Time
  		key :active,      	Boolean
  		key :actor_ids,		Array

		many :authors, :in => :author_ids
	end
