# A MongoDB, MongoMapper, Rails 3 Sample Application and Tutorial

The purpose of this tutorial is to provide step-by-step instruction on how to build a Rails 3 Application using MongoDB and MongoMapper. 

MongoDB is a NoSQL database that can be used as a replacement for SqlLite, MySQL, Postgresql or other sql databases.  NoSQL is a great alternative for web applications where scalability is required or data must be grouped together in greater numbers than the hard limits applied by sql-based databases.

Basic Ruby and Rails 3 knowledge will be required to follow this tutorial.

## Prerequisites:

You must have the following installed on your system.

*  Ruby 1.9.2
*  Rails 3.1
*  MongoDB 2.0.2
*  Git

# Sample Application

The sample application may be downloaded using the following terminal command:

	$ git clone git@github.com:tyrauber/mongodb-rails3-tutorial.git

On by clicking on the following link: https://github.com/tyrauber/mongodb-rails3-tutorial/zipball/master

Navigate to the application directory:

	$ cd mongodb-rails3-tutorial/

To run a Rails 3 application, like this, type the following:

	$ rails server

# Tutorial: Creating a MongoDB, MongoMapper Rails 3 Application

The following tutorial results in the sample application included in this repository.

###  Step 1: Build a rails app using the  --skip-active-record option

The very first thing we are going to do is build the Rails 3 Application framework. Since MongoDB isn't a SQL database, it does not use ActiveRecord to store data. We will instead be using the MongoMapper gem as a replacement for ActiveRecord.  Therefore, we will need to use '--skip-active-record' when generating our Rails 3 Application.

	$ rails new mongodb-rails3-tutorial --skip-active-record

Navigate to the application directory:

	$ cd mongodb-rails3-tutorial/

### Step 2: Modify the Gemfile

Now that we have generated our Rails 3 application framework, we should modify our Gemfile to include the necessary gems.

To do so, we need to open GEMFILE in your favorite text editor. I prefer using nano from the command line:

	$ nano GEMFILE

At the top of the GEMFILE, you should see the following:

	source 'http://rubygems.org'

Require both 'rubygems' and 'mongo' by adding the following under "source 'http://rubygems.org":

	require 'rubygems'
	require 'mongo'

Include the mongo_mapper gem, after the rails gem:

	gem 'rails', '3.1.1'
	gem 'mongo_mapper'

In total, the top of our Gemfile should look something like this:

	source 'http://rubygems.org'

	require 'rubygems'
	require 'mongo'

	gem 'rails', '3.1.1'
	gem 'mongo_mapper'

Save and exit the GEMFILE. In nano, type CONTROL-x, y, ENTER.

### Step 3:  Run the bundle installer:

Now install the required gems, by typing at the command prompt:

	$ bundle install

If successful, you should see a list of gems and at the end of the list the declaration, "Your bundle is complete!"

If for some reason this process fails, you may install a gem manually - for example 'mongodb' - through the following terminal command:

	$ gem install mongodb

You can rerun  'bundle install' to confirm successful gem installation.  

If you are unfamiliar with gems, the following commands are also handy:

*	$ gem install name-of-gem		#Installs a gem
*	$ gem uninstall name-of-gem 		#Uninstalls a gem
*	$ gem list	 				# Lists all gems installed in your system

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

Save and exit mongo.rb.

### Step 7: Create a MongoDB Rake Task

We should also add a MongoDB Rake task to make our tests MongoDB-aware. At the command prompt:

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

# Functionality Cheatsheet

This part of the tutorial is written to explain MongoDB specific changes to the Rails 3 development process.  The sample commands and code provided is for educational purposes and will not corrospond to files in the sample applicaion.

## Modeling with MongoDB and MongoMapper in Rails 3

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

You may alternatively declare a Model an EmbeddedDocument (a document stored within another document):

	include MongoMapper::EmbeddedDocument

More on this later.

### Key-Based Data Schema

In MongoDB, data is stored in json-formatted document storage as opposed to a relational database. Because there isn't ActiveRecord or Migrations, use MongoMapper to define our data schema in the model directly.  To do so, make key declarations, like so:

	key :title, String

This would mean that in the model there would be a keypair with the key 'title' and a string value.

### Data Types

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

### Indexing

To improve the performance of querying in MongoDB, use indexing.  Indexing is defined in: config/intitializers/database.rb

To index a single key, for example, title in our movie model, we'd do the following:

	Movie.ensure_index(:title)

To ensure uniqueness, we would add the ":unique => true" option:

	Movie.ensure_index(:title, :unique => true)

To index multiple keys, for example, genre and title:

	 Movie.ensure_index([[:genre, 1], [:title, -1]], :unique => true) 

The 1 determines an ascending sort order, while the -1 determines a descending sort order.

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

For example:

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

## Querying with MongoMapper in Rails 3

Querying with MongoMapper is similiar to querying with ActiveRecord.

###  Find All

To find all documents associated with a model, do:

	ModelName.all

For example, with a model named Movie:

	Movie.all

Would return all movie documents.

###  Find All With Conditions

To find documents with specific conditions, for example all movies made in the year 2011:

	Movie.all(:year => '2011')

### Find First, Last

Find the first record created, for example the first movie document created:

	Movie.first

Or:

	Movie.all(:first)

To find the last:

	Movie.last

Or:

	Movie.all(:last)

### Find Key or Keys

To find records by a specific key, use:

	Model.find_by_key_name(value)

To find the movie Die Hard in our movie example:

	Movie.find_by_title("Die Hard")

To find all records by a specific key, use:

	Model.find_all_by_key_name(value)

To find all records with multiple key values, for example every movie made in 2011 with a running time of 110 minutes:

	Model.find_all_by_year_and_minutes(2011,110)

## More Information

This tutorial was meant to provide a crash course on MongoDB, MongoMapper and Rails 3. To learn more:

* 	About MongoDB, visit:		http://www.mongodb.org/display/DOCS/Ruby+Language+Center
* 	About MongoMapper, visit:	http://mongomapper.com/documentation/
*	About Rails 3, visit:		http://ruby.railstutorial.org/ruby-on-rails-tutorial-book
	


