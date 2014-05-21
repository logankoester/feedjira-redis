# feedjira-redis

[![Build Status](http://ci.ldk.io/logankoester/feedjira-redis/badge)](http://ci.ldk.io/logankoester/feedjira-redis/)
[![Gem Version](https://badge.fury.io/rb/feedjira-redis.svg)](http://badge.fury.io/rb/feedjira-redis)

FeedjiraRedis uses Datamapper's dm-redis-adapter to provide a persistance layer for the [feedjira](https://github.com/feedjira/feedjira) RSS/Atom feed consumption library.

## Example Usage

FeedjiraRedis can be used in much the same fashion as [feedjira](https://github.com/feedjira/feedjira) itself. One common use for FeedjiraRedis is to avoid updating the feed with every page hit, so you could update the feed from a Rakefile on a cron job instead.

#### Shared

    DataMapper.setup(:default, { :adapter  => 'redis' })
    FEED_URL = 'https://github.com/logankoester/feedjira-redis/commits/master.atom'

#### Rakefile

    namespace :blog do
      desc 'Grab the latest blog posts'
      task :update do
        FeedjiraRedis::Feed.fetch_and_parse(FEED_URL)
      end
    end

FeedjiraRedis::Feed is actually a Datamapper model that wraps the interface to Feedjira::Feed with some code to add persistence. Feeds are uniquely identified by their `feed_url` property, so when you call `fetch_and_parse` or `update` a second time the new entries are associated with the original feed.

Run `rake blog:update` to grab some entries.

#### Action
Now you just need to fetch the object your Rakefile created.

`@feed = FeedjiraRedis::Feed.first(:feed_url => FEED_URL)`

#### View
FeedjiraRedis::Entry is also a Datamapper model, but it shares the same properties as Feedjira::Entry.

    %h1= @feed.title
      - @feed.entries.each do |e|
        %h2= e.title
        %p= e.content

See the [Feedjira](https://github.com/feedjira/feedjira) documentation or [read the code](https://github.com/logankoester/feedjira-redis/blob/master/lib/feedjira-redis.rb) (it's short!) for further details.

## Contributing to feedjira-redis
 
* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it in a future version unintentionally.
* Please try not to mess with the Rakefile, version, or history. If you want to have your own version, or is otherwise necessary, that is fine, but please isolate to its own commit so I can cherry-pick around it.

## Copyright

Copyright (c) 2011-2014 Logan Koester. See LICENSE.txt for
further details.
