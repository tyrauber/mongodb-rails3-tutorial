# Rails 3 / MongoDB Tutorial

The purpose of this tutorial is to show step-by-step how to build a Rails 3 Application using MongoDB and MongoMapper. 

MongoDB is a NoSQL database that can be used as a replacement for SqlLite, MySQL, Postgresql or other sql databases.  NoSQL is a great alternative for web applications where scalability is required or data must be grouped together in greater numbers than the hard limits applied by sql-based databases.

Basic Ruby and Rails 3 knowledge will be required to follow this tutorial.

## Prerequisites:

You must have the following installed on your system.

*  Ruby 1.9.2
*  Rails 3.1
*  MongoDB 2.0.2
*  Git

## Download the Example Application

The complete application can be downloaded using the following terminal command:

	$ git clone git@github.com:tyrauber/mongodb-rails3-tutorial.git

To run the Rails application, type the following at the command prompt:

	$ cd mongodb-rails3-tutorial/

To run a Rails 3 application, this, type the following:

	$ rails server

# What to build?

I am a media-junkie -  I love making media, I love consuming media - But I can't stand advertising.  So instead of subscribing to Cable Television, I get my media-fix through NetFlix Instant.
 
Therefore, I thought it might be fun to build something NetFlix related. 

For this tutorial, we will be creating a NetFlix Instant New Releases Application that provides an simple method for browsing though NetFlix Instant's new releases. 

Each movie should have an image, title, description and a link to watch the movie.

The NetFlix API would be entirely too complex for our needs. so we will be using the following feed, curtesy of TheNowhereMan.com:

	feeds.feedburner.com/NF-InstantTitlesThisWeek

The application will grab the xml feed using the 'rest-client', parse the xml using using the 'hpricot' gem and then cache the data in our MongoDB using MongoMapper.

So, let's begin.

#



