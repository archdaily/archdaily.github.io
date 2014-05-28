---
layout: post
title:  "I18n tips and tricks"
author: Mauricio Ulloa
date:   2014-04-30 13:00:00
categories: i18n
tags: ror good-practices
---

When I began internationalizing an application, I started wondering which were the good practices. After reading some tips in many blogs I concluded that there's no silver bullet, but there are some tricks that can simplify your work. Here are some tips and tricks that I've learned:

## Choosing locales names

For choosing the names of the locales that you are going to use, the best is to follow the standard defined in _Sven Fuchs_'s repository [rails-i18n][rails-i18n].

## Setting the site in the URL

One option for setting the site in the application is using the URL (e.g. `innovation.archdaily.com/us`). In my opinion this is a very transparent approach and [it's REST friendly][REST-friendly]. The best way to set the site in the URL is using routing scopes:

```ruby
App::Application.routes.draw do
	...
	scope '/:site',
		get '/' => 'main_controller#index'
	end
	...
end
```

## Defining supported sites

Using scopes in routing makes easier to define the supported sites. If you are supporting `us` and `cl`  you can just put it this way in the routing file:

```ruby
App::Application.routes.draw do
	...
	scope '/:site', locale: /us|cl/ do
		get '/' => 'main_controller#index'
	end
	...
end
```

## Redirecting legacy URLs

If your application is already running you may have many routes that are going to get broken with the new URLs. There's a very simple way to redirect the URL in the routing file, using `redirect` outside the `scope`. For example:

```ruby
App::Application.routes.draw do
	...
	scope '/:site', locale: /us|cl/ do
		get '/' => 'main_controller#index'
	end

	get '/old/url' => redirect('/us/old/url')
	get '/vieja/url' => redirect('/cl/old/url')
	...
end
```

## Setting the locale from the URL

Now that you support the site in the URL you should set the locale in your application. A very common and simple solution is setting the locale in a `before_filter` inside `application_controller.rb`:

```ruby
class ApplicationController < ActionController::Base
	...
	before_filter :set_locale

	def set_locale
		case params[:site]
		when 'cl'
			locale = 'es-CL'
		else
			locale = 'en-US'
		end
		I18n.locale = locale
	end
	...
end
```

## But now the URL helpers doesn't work ...

After defining the new routes, the helpers are going to return broken URLs. This happens because they assume that the resource's path is the old one. A fix that some developers do is the following:

```ruby
link_to @product.title , product_url(@product, site: params[:site])
```

This fix works but you must do it for every `product_url`, `product_path`, `user_url`, `user_path`, etc... inside your application. There is a cleaner and more maintainable way to solve this issue, to add a `default_url_options` method in:

```ruby
class ApplicationController < ActionController::Base
	...
	before_filter :set_locale

	def set_locale
		case params[:site]
		when 'cl'
			locale = 'es-CL'
		else
			locale = 'en-US'
		end
		I18n.locale = locale
	end

	def default_url_options
		{ site: params[:site] || 'us' }
	end
	...
end
```

## Setting up a fallback

Sometimes it's mandatory to have fallbacks for missing translations. The best approach that I've found is from _Bozhidar Batsov_'s [blog][batsov-post], who gives three different options for setting it in `application.rb`:

1- Fallback to what's specified in `config.i18n.default_locale`.

```ruby
class Application < Rails::Application
	...
	config.i18n.fallbacks = true
	...
end
```

2- Fallback to a locale, regardless of what's in `config.i18n.default_locale`.

```ruby
class Application < Rails::Application
	...
	config.i18n.fallbacks = [:en]
	...
end
```

3- Specify a fallback map.

```ruby
class Application < Rails::Application
	...
	config.i18n.fallbacks = {'es' => 'en', 'fr' => 'en', 'de' => 'fr'}
	...
end
```

## Creating translated views

Sometimes you will need different views for each localization. The good practice says that you should **totally avoid this** because is much harder to maintain than using the `t(' ')` function. [Here are some tips][rails-i18n-tips] for using `yml` and `t(' ')` function like a pro.

A very frequent problem is to have different css for different locales. There are two approaches for solving this: the first one is to use the [`:lang` css selector][lang-css-document] (which works pretty well in font related styles) and the second one is to localize the css class (e.g. `<div class="<%= t('class-name') %>">`).

If you can't avoid having translated views, the good practice is naming the views `name.locale.html.erb`. When you follow this format you can call the view `<%= render name %>` and the corresponding view will be rendered by default. The locale will be taken from `I18n.locale`.

## Using multiple YML files

If you want to improve the modularity of your application you can create multiple YML files, one for each module of your application. Rails facilitates this loading all the YML files present in `/config/locales`. So you can split your translation from `en.yml` to `admin.en.yml` and `landing.en.yml`.

If you have more tips and tricks about I18n you can contact us.

[rails-i18n]: https://github.com/svenfuchs/rails-i18n/tree/master/rails/locale
[REST-friendly]: http://restpatterns.org/Articles/HTTP_i18N_Patterns
[rails-i18n-tips]: http://thepugautomatic.com/2012/07/rails-i18n-tips
[batsov-post]: http://batsov.com/articles/2012/09/12/setting-up-fallback-locale-s-in-rails-3
[lang-css-document]: http://www.w3schools.com/cssref/sel_lang.asp
