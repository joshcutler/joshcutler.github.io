---
layout: post
title: "Bullet Plugin for Rails"
date: 2009-11-03 13:10 -0400
tags: rails performance bullet
---

Recently I have been working on improving the performance in one of my clients Rails applications, trying to get page load times down.  Looking at the query log it was obvious that there was way too much activity going on for what was being rendered on the page–there were [N+1 queries](http://stackoverflow.com/questions/97197/what-is-the-n1-selects-problem) all over the place.  This was nothing a few joins (or [eager loads](http://guides.rubyonrails.org/active_record_querying.html#eager-loading-associations) as the rails folks like to say) wouldn’t fix.  So after changing the queries to make sure that all the necessary data was gathered in just one pass, I wondered how many other places this was cropping up in the application. That is when I stumbled upon [flyerhzm](http://github.com/flyerhzm)‘s [Bullet Gem](http://github.com/flyerhzm/bullet).


In short the gem monitors the queries that your server is making and notifies you as to whether or not you are encountering any N+1 scenarios.  It also notifies you as to whether or not you have unused eager loads.  It can be configured to notify you via javascript alerts, growl notifications, firebug notifications, or just log the instances.


Thus far it has been invaluable in finding instances where an eager load would help.  It can (and should) be configured to run only in Development mode and will sit in the background until it has something to notify you about, so you can just turn it on, keep coding, and as you browse around your local instance, get notified as to potential areas for optimization.


While it is definitely worth installing a few things to note:

1. You still need to use your judgement as to what is the right call, this just points out areas to investigate (though it does tell you what models to include)
2. It has some bugs still.  There are instances where it correctly insists that you should include something, and then incorrectly tells you that you have an unused eager load once you include it


If you have your development.rb file in source control and shared among developers, one slight modification to the code on the github site that removes the gem dependency from your fellow developers is as follows:

```ruby
config.after_initialize do
    begin
        require 'bullet'
        Bullet.enable = false
        Bullet.alert = false
        Bullet.bullet_logger = true
        Bullet.console = true
        Bullet.rails_logger = true
        Bullet.disable_browser_cache = true
        begin
            require 'ruby-growl'
            Bullet.growl = true
        rescue MissingSourceFile
        end
    rescue MissingSourceFile
    end
end
```