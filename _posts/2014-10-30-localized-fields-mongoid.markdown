---
layout: post
title:  "Localized fields in Mongoid"
author: Sebastian Machuca
date:   2014-10-30 11:00
categories: mongoid locale localized_fields
---

Sometimes we need store localized data in our MongoDB database, maybe one way to do that is create a 'Translations' table, where we store the localized fields.
Another way is create multiple fields like "name\_en", "name\_fr"...

But Mongoid has a 'better & simple' way to do that...

###"Localized Fields" by Mongoid from v2.4.0
1. Add 'localize' flag to the field
<pre><code>Class Post
      include Mongoid::Document
      field :name, localize: true
    end
</code></pre>
2. Set the locale before assign the field value
 <pre><code>post = Post.new
    I18n.default_locale = :en
    post.name = "Little Duck"
    I18n.default_locale = :es
    post.name = "Patito"
    post.save
</code></pre>

3. The result
<pre><code>post.name_translations
    => { "en" => "Little Duck", "es" => "Patito" }
</pre></code>

4. Create custom get/set method:
<pre><code>%w( en es fr ).each do |lang|
      define\_method("name\_#{lang}") do
        I18n.locale = lang.to\_sym
        name = self.name
        I18n.locale = :en 
        name
      end
      define\_method("name\_#{lang}=") do |argument|
        I18n.locale = lang.to\_sym
        self.name= argument
        name = self.name
        I18n.locale = :en 
        name
      end
    end
</pre></code>

###Some 'dirty' things
- You **must** change the application locale every time.
- To search across the languages, you have to create a index in the model.

### [Official Mongoid Doc](http://mongoid.org/en/mongoid/v3/documents.html#localized_fields)
