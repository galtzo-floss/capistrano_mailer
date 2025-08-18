# 📬 CapistranoMailer

A Gem For Capistrano Deployment Email Notification

| Project                 |  CapistranoMailer    |
|------------------------ | ----------------- |
| gem name                |  capistrano_mailer   |
| license                 |  MIT              |
| expert support          |  [![Get help on Codementor](https://cdn.codementor.io/badges/get_help_github.svg)](https://www.codementor.io/peterboling?utm_source=github&utm_medium=button&utm_term=peterboling&utm_campaign=github) |
| download rank               |  [![Total Downloads](https://img.shields.io/gem/rt/capistrano_mailer.svg)](https://rubygems.org/gems/capistrano_mailer) |
| version                 |  [![Gem Version](https://badge.fury.io/rb/capistrano_mailer.png)](http://badge.fury.io/rb/capistrano_mailer) |
| dependencies            |  [![Dependency Status](https://gemnasium.com/pboling/capistrano_mailer.png)](https://gemnasium.com/pboling/capistrano_mailer) |
| code quality            |  [![Code Climate](https://codeclimate.com/github/pboling/capistrano_mailer.png)](https://codeclimate.com/github/pboling/capistrano_mailer) |
| inline documenation     |  [![Inline docs](http://inch-ci.org/github/pboling/capistrano_mailer.png)](http://inch-ci.org/github/pboling/capistrano_mailer) |
| continuous integration  |  [![Build Status](https://secure.travis-ci.org/pboling/capistrano_mailer.png?branch=master)](https://travis-ci.org/pboling/capistrano_mailer) |
| test coverage           |  [![Coverage Status](https://coveralls.io/repos/pboling/capistrano_mailer/badge.png)](https://coveralls.io/r/pboling/capistrano_mailer) |
| homepage                |  [on Github.com][homepage] |
| documentation           |  [on RubyDoc.info][documentation] |
| live chat               |  [![Join the chat at https://gitter.im/pboling/capistrano_mailer](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/pboling/capistrano_mailer?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) |
| Spread ~♡ⓛⓞⓥⓔ♡~      |  [🌏](https://about.me/peter.boling), [👼](https://angel.co/peter-boling), [:shipit:](http://coderwall.com/pboling), [![Tweet Peter](https://img.shields.io/twitter/follow/galtzo.svg?style=social&label=Follow)](http://twitter.com/galtzo) |

[semver]: http://semver.org/
[pvc]: http://docs.rubygems.org/read/chapter/16#page74
[railsbling]: http://www.railsbling.com
[peterboling]: http://www.peterboling.com
[coderbits]: https://coderbits.com/pboling
[coderwall]: http://coderwall.com/pboling
[documentation]: http://rubydoc.info/gems/capistrano_mailer
[homepage]: https://github.com/pboling/capistrano_mailer

## Summary

  * It is a Capistrano Plugin / Ruby Gem that requires ActionMailer
  * It is MIT licensed
  * It is old as duck, and it might be almost dead, maybe. (I started this project in 2007)
  * rails2 branch, releases will be in 3.x range, requires Rails 2.X
  * master branch, releases will be in 4.x range, requires Rails 3.X
  * Requires at least Capistrano 2.4.3 (might work with capistrano as old as 2.1.0, but has not been tested)
  * Known to be compatible with SCMs as of version 3.1.2: Perforce, SVN, and Git
  * Known to be compatible with, but does not require, the deprec gem.

## About

Ever wanted to be emailed whenever someone on the team does a cap deploy of trunk or some tag to some server.
Wouldn't it be nice to know about it every time a release was deployed?  For large rails projects this type of coordination is essential,
and this plugin makes sure everyone on the need to know list is notified when something new is deployed.

This plugin/gem is an extension to Capistrano.

That means it registers itself with Capistrano as a plugin and is therefore available to call in your recipes.

If you are looking to roll your own email integration into capistrano then try this pastie:
http://pastie.org/146264 (thanks to Mislav Marohnić).
But if you want to take the easy road to riches then keep reading ;)
 -- figurative "riches" of course, I promise nothing in return for your using this plugin

### Important Note:

The first time you deploy to a server (a 'cold' deploy) capistrano mailer will cause an error because it uses capistrano's previous release variables, and when there are no previous releases capistrano throws an error.  In the next version this will be fixed, just don't have time at the moment.  If you would like to work on this 'first deploy' problem please fork my repo and work on it!

## Installation

Add this line to your application's Gemfile:

    gem 'capistrano_mailer'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install capistrano_mailer

### With Rails >= 3.x.x

The 4.x+ versions of this gem require at least Rails 3

### With Rails <= 2.3.x

Switch to the rails2 branch (releases will be in 3.x range)


## Usage

1) You need to have already setup capistrano in the project, including the 'capify .' command.

2) Add this line to the top of your config/deploy.rb:

    # For plugin:
    #   You must make capistrano_mailer's libraries available in Ruby's load path.  This is one way to do that:
    #   Add to the top of your config/deploy.rb file:
    $:.unshift 'vendor/plugins/capistrano_mailer/lib'

    # For frozen gem:
    #   You must make capistrano_mailer's libraries available in Ruby's load path.  This is one way to do that:
    #   Add to the top of your config/deploy.rb file:
    $:.unshift 'vendor/gems/capistrano_mailer-x.x.x/lib'

    # then for gem or plugin:
    ####################################
    # Capistrano Plugins go here
    require 'capistrano/mailer'
    #configure capistrano_mailer:
    # The configuration file can go anywhere, but in past versions of the gem it was required to be in the config/ dir.
    require 'config/cap_mailer_settings'
    ####################################

3) Configure Caistrano Mailer in the settings file required in step 2:

    # If installed as a plugin might need the require here as well

    ActionMailer::Base.delivery_method = :smtp # or :sendmail, or whatever
    ActionMailer::Base.smtp_settings = { # if using :smtp
        :address        => "mail.example.com",
        :port           => 25,
        :domain         => 'default.com',
        :perform_deliveries => true,
        :user_name      => "releases@example.com",
        :password       => "mypassword",
        :authentication => :login }
    ActionMailer::Base.default_charset = "utf-8"# or "latin1" or whatever you are using

    CapMailer.configure do |config|
      config[:recipient_addresses]  = ["dev1@example.com"]
      # NOTE: THERE IS A BUG IN RAILS 2.3.3 which forces us to NOT use anything but a simple email address string for the sender address.
      # https://rails.lighthouseapp.com/projects/8994/tickets/2340
      # Therefore %("Capistrano Deployment" <releases@example.com>) style addresses may not work in Rails 2.3.3
      config[:sender_address]       = "deployment@example.com"
      config[:subject_prepend]      = "[EMPTY-CAP-DEPLOY]"
      config[:site_name]            = "Empty Example.com App"
    end

4) Add these two tasks to your deploy.rb:

    namespace :show do
      desc "Show some internal Cap-Fu: What's mah NAYM?!?"
      task :me do
        set :task_name, task_call_frames.first.task.fully_qualified_name
        #puts "Running #{task_name} task"
      end
    end

    namespace :deploy do
      ...

      desc "Send email notification of deployment (only send variables you want to be in the email)"
      task :notify, :roles => :app do
        show.me  # this sets the task_name variable
        mailer.send_notification_email(self)
      end

      ...
    end

5) Make _sure_ you've defined `rails_env`, `repository`, `deploy_to`, `host`, and `application`.  `task_name` is defined by the show:me task above, and the others are defined behind the scenes by Capistrano!

6) The only parameter to mailer.send_notification_email that is *required* is the first. _Minimally_ you need to define the capistrano variables:

    :rails_env
    :repository
    :task_name (provided by the show:me task included in this readme)
    :deploy_to
    :host
    :application

But there are tons of others - just take a look at lib/mailer/cap_mailer.rb.

If anyone has a cool way of recording the *output* into a capistrano accessible variable,
so that it can be shoved into the release email that would be an excellent contribution!

7) Then add the hook somewhere in your deploy.rb:

    after "deploy", "deploy:notify"

8) Enjoy and Happy Capping!

## Customization

If you want to use your own views you'll need to recreate the notification_email view:
First you need to define where your templates are:

    CapMailer.configure_capistrano_mailer do |config|
      config[:template_root]      = "app/views/capistrano_mailer/"
    end

Then you'll need to create templates there called:

  `notification_email.text.html.erb`

and / or

  `notification_email.text.plain.erb`

Take a look at the templates that comes with the plugin to see how it is done (views/cap_mailer/...)

## Credit where Credit is Due

[Peter Boling (pboling)](https://github.com/pboling) - Wrote original & maintainer
[Dave Nolan (textgoeshere)](https://github.com/textgoeshere) - lots of refactoring for 3.2 release
[Jason Rust (jrust)](https://github.com/jrust) - Updated for Rails 3 compatibility

Thanks to [Dustin Deyoung (ddeyoung)](https://github.com/ddeyoung) for the beautiful HTML email templates.
Thanks to mixonix and [Yoan Blanc (greut)](https://github.com/greut) for work on SCMs compatibility

## How you can help!

Take a look at the `reek` list which is the file called `REEK` and stat fixing things.  Once you complete a change, run the tests:

```
bundle exec rake test:all
```

If the tests pass refresh the `reek` list:

```
bundle exec rake reek > REEK
```

Follow the instructions for "Contributing" below.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Versioning

This library aims to adhere to [Semantic Versioning 2.0.0][semver].
Violations of this scheme should be reported as bugs. Specifically,
if a minor or patch version is released that breaks backward
compatibility, a new version should be immediately released that
restores compatibility. Breaking changes to the public API will
only be introduced with new major versions.

As a result of this policy, you can (and should) specify a
dependency on this gem using the [Pessimistic Version Constraint][pvc] with two digits of precision.

For example:

    spec.add_dependency 'capistrano_mailer', '~> 0.5'

## License

* MIT License - See LICENSE file in this project
* Copyright (c) 2008-2014 [Peter H. Boling][peterboling] of [Rails Bling][railsbling]
* Copyright (c) 2007-8 Peter Boling & Sagebit, LLC
