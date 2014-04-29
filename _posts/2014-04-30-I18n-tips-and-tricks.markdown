---
layout: post
title:  "I18n tips and tricks"
author: Mauricio Ulloa
date:   2014-04-30 13:00:00
categories: Softwre-Engineering i18n ror good-practices
---

When I began internationalizing an application, I started wondering which were the good practices. After reading some tips in many blogs I concluded that there's no silver bullet, but there are some tricks that can simplify your work. Here are some tips and tricks that I've learned:

## Setting the locale in the URL

One option for setting the locale in the application is using the URL (e.g. `innovation.archdaily.com/us`). I my opinion this is a very transpartent approach and [it's REST friendly][REST-friendly]. The best way to set the locale in the URL is using routing scopes:

```ruby
App::Application.routes.draw do
	...
	scope '/:locale', 
		get '/' => 'main_controller#index'
	end
	...
end
```

## Defining default and supported locales

Using scopes in routing to define the language makes easier to define the default and supported locale. If you are supporting `en` and `es`, and the default locale is `en` you can just put it this way in the routing file:

```ruby
App::Application.routes.draw do
	...
	scope '/:locale', locale: /en|es/, defaults: {locale: 'en'} do
		get '/' => 'main_controller#index'
	end
	...
end
```

## Redirecting legacy URLs

If your application is already running you may have many routes that are going to be broken with the new URLs. There's a very simple way to redirect the URL in the routing file, using `redirect` outside the `scope`. For example:

```ruby
App::Application.routes.draw do
	...
	scope '/:locale', locale: /en|es/, defaults: {locale: 'en'} do
		get '/' => 'main_controller#index'
	end

	get '/old/url' => redirect('/en/old/url')
	get '/vieja/url' => redirect('/es/old/url')
	...
end
```

## Setting the locale from the URL

Now that you support the locale in the URL you should set it in your application. A very common and simple solution is setting the locale in a `before_filter` inside `application_controller.rb`:

```ruby	
class ApplicationController < ActionController::Base
	...
	before_filter :set_locale

	def set_locale
 		I18n.locale = params[:locale]
	end
	...
end
```

## But now the URL hepers doesn't work ...

After defining the new routes, the helpers are going to return broken URLs. This happens because they assume that the resource's path is the old one. A fix that some developers do is the following:

```ruby	
link_to @product.title , product_url(@product, locale: params[:locale])
```

This fix works but you must do it for every `product_url`, `product_path`, `user_url`, `user_path`, etc... inside your application. There is a cleaner and more maintainable way to solve this issue, in the method that is setting the locale you can add `Rails.application.routes.default_url_options[:locale]= I18n.locale`:

```ruby	
class ApplicationController < ActionController::Base
	...
	before_filter :set_locale

	def set_locale
 		I18n.locale = params[:locale]
 		Rails.application.routes.default_url_options[:locale]= I18n.locale
	end
	...
end
```

## Creating translated views

Sometimes you will need different views for each localization. The good practice says that you should **totally avoid this** because is much harder to mantain than using the `t(' ')` function. [Here are some tips][rails-i18n-tips] for using `yml` and `t(' ')` function like a pro. 

If you can't avoid having translated views, the good practice is naming the views `name.locale.html.erb`. When you follow this format you can call the view `<%= render name %>` and the corresponding view will be rendered by default. The locale will be taken from `params[:locale]`.

## Using multiple YML files

If you want to improve the modularity of your application you can create multiple YML files, one for each module of your application. Rails facilitates this loading all the YML files present in `/config/locales`. So you can split your translation from `en.yml` to `admin.en.yml` and `landing.en.yml`.

If you have more tips and tricks about I18n you can contact us.

[REST-friendly]: http://restpatterns.org/Articles/HTTP_i18N_Patterns
[rails-i18n-tips]: http://thepugautomatic.com/2012/07/rails-i18n-tips/