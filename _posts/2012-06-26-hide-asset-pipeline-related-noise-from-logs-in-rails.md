---
layout: post
title: "Hide asset pipeline related noise from logs in Rails"
description: ""
category: [Rails, logging] 
tags: [howto, Rails, logging]
---
{% include JB/setup %}

It is extremely annoying to scroll through pages and pages of output on
command line of asset pipeline related stuff to find useful stuff.
Thanks to a colleague [Sohaib Bhatti](http://sohaibbbhatti.wordpress.com/) here
is a protip to hide asset pipeline related noise from appering in logs.

    if Rails.env.development?
     Rails.application.assets.logger = Logger.new('/dev/null')
      Rails::Rack::Logger.class_eval do
        def call_with_quiet_assets(env)
          previous_level = Rails.logger.level
          Rails.logger.level = Logger::ERROR if env['PATH_INFO'] =~ %r{^/assets/}
          call_without_quiet_assets(env)
        ensure
          Rails.logger.level = previous_level
        end
        alias_method_chain :call, :quiet_assets
      end
    end

Thats it!
