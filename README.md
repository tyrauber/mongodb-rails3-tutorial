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

# The CliffNotes Version

The down and dirty, for experienced Rails 3 developers:

	* $ rails new app-name --skip-active-record 	#This is important because it doesn't add the hooks for ActiveRecord to your Rails App. 
	* Add "source 'http://gemcutter.org'", "require 'rubygems'", "require 'mongo'" and "gem 'mongomapper'" to your GEMFILE before running $ bundle install.
	* $ gem install bson_ext 	#Adds the c-extensions to improve performance.
	* Add a mongo initializer 	# See part 1, step 6
	* Add a mongo rake task		# See part 1, step 7
	* No DB Migrations, MongoDB doesn't use them
	* No "< ActiveRecord::Base" in your Model Class declaration.	# See part 2, 
	* Add "include MongoMapper::Document" to the top of your model.
	* Declare your fields in your model "key :title,   String"

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

	*  $ gem install name-of-gem		#Installs a gem
	*  $ gem uninstall name-of-gem 		#Uninstalls a gem
	*  $ gem list 				# Lists all gems installed in your system

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

### Step 8:  Quiet Asset Chain Logging

This step isn't really necessary, or even applicable to MongoDB, but is pertinent to Rails 3.  In Rails 3, the images, stylesheets and javascript folders are moved to an asset directory with the app directory.  As a result, every call to an image, stylesheet or javascript file is logged in the console, making it tiresome to use the log in application development.

Therefore, in Rails 3 applications, I like to create an intializer, through the following terminal command:

	$ nano config/initializers/quiet_assets.rb

Add the following code:

	Rails.application.assets.logger = Logger.new('/dev/null')
	Rails::Rack::Logger.class_eval do
  		def call_with_quiet_assets(env)
   	 		previous_level = Rails.logger.level
   			Rails.logger.level = Logger::ERROR if env['PATH_INFO'].index("/assets/") == 0
    			call_without_quiet_assets(env).tap do
      				Rails.logger.level = previous_level
    			end
  		end
  		alias_method_chain :call, :quiet_assets
	end

This technique was discovered here: http://stackoverflow.com/questions/6312448/how-to-disable-logging-of-asset-pipeline-sprockets-messages-in-rails-3-1

Special thanks to stackoverflow and choonkeat.

### Step 9: Run the server

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

## Part 2:  Adding Functionality to our MongoDB, Rails 3 Application

