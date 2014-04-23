---
layout:     post
title:      "A few tips to speed up RSPEC: Stubbing and Mocking"
author:     Gustavo Garcia G
date:       2014-04-23 09:00:00
categories: rails
tags:       rpsec rails tests
---

A few days ago, I took a [course about RSPEC][rspec-codeschool]. This post is about the two things I enjoyed most: Stubbing and Mocking.

Stubbing is a way to fake a call to a method. Sometimes we need to test a method A that calls another (very huge) method B inside, and testing method A should test just method A, not method B, right?

Mocking is an expectation of a Stub getting called.

Let's see some code now.

<!-- more -->

Stubbing
========

We are going to asume for now that we have the following *class Article*:

```ruby
class Article < ActiveRecord::Base
  ...
  def schedule
    self.export
    self.scheduled = true
  end

  def export
    #A very expensive method    
  end
  ...
``` 

Now, this is how the *schedule* test should look like:

```ruby
describe Article do
  context "#schedule" do
    it "sets scheduled as true" do
      article = FactoryGirl.create(:article)
      article.schedule
      expect(article.scheduled).to be true
    end
  end
end

```

The test will probably pass, but we are calling the expensive method *export* for this test... is this really necessary? Maybe not. Here is where we could use a **Stub**, just like this:

```ruby
describe Article do
  context "#schedule" do
    it "sets scheduled as true" do
      article = FactoryGirl.create(:article)
      allow(article).to receive(:export)
      article.schedule
      expect(article.scheduled).to be true
    end
  end
end

```

The test will still pass, but we feel smarter now, because we are not calling the real method *export*. What do we gain? A faster test suit.

Ok, but this was an easy escenario, let's asume this *Article* class refactorization:

```ruby
class Article < ActiveRecord::Base
  ...
  def schedule
    self.scheduled = self.export
  end

  def export
    #A very expensive method that returns true/false, depending of success or failure at exporting
    ...    
  end
  ...
``` 

Our previous stub will make the test fail, because our fake method isn't returnig true, so... let's fix it!

```ruby
describe Article do
  context "#schedule" do
    it "sets scheduled as true" do
      article = FactoryGirl.create(:article)
      allow(article).to receive(:export).and_return(true)
      expect(article.scheduled).to be true
      article.schedule
    end
  end
end
```

Voila! The test pass again. Note that you can specify anything as a return value. In this case we used a boolean, but  I have used OpenStruct too.

Mockings
========

In some cases, we need more than overwritting a method, we need to make sure that this fake method is being called, here is where we use *Mocks*.

In the case we need to make sure the method *export* is being called in certain scenario, we can try this

```ruby
describe Article do
  context "#schedule" do
    it "sets scheduled as true" do
      article = FactoryGirl.create(:article)
      allow(article).to receive(:export).and_return(true)
      expect(article).to receive(:export)
      article.schedule
    end
  end
end
```

We can be even more specific, an use *receive(:export).exactly(N).times, like this:

```ruby
describe Article do
  context "#schedule" do
    it "sets scheduled as true" do
      article = FactoryGirl.create(:article)
      allow(article).to receive(:export).and_return(true)
      expect(article).to receive(:export).exactly(1).times
      article.schedule
    end
  end
end
```

This mockings are very useful when you have conditions inside your methods, so you can test some scenarios with different conditions and test how many times the method is called.

Another hint is that you can also stub/mock static methods, like this:

```ruby
class Article < ActiveRecord::Base
  ...
  def schedule
    self.export
    self.scheduled = true
  end

  def export
    #A very expensive method    
  end

  self.get_exported
    #a static method
  end
``` 
And the test:

```ruby
describe Article do
  context "#schedule" do
    it "sets scheduled as true" do
      article = FactoryGirl.create(:article)
      allow(Article).to receive(:get_exported)
      expect(Article).to receive(:get_exported).exactly(1).times
      article.schedule
    end
  end
end
```

Extra Tips
==========

1 - To mark a test as pending, you can just insert a "x" before the "**it**" statement, like this:

```ruby
xit "sets scheduled as true" do
```

2 - To run just a specific test, you can run this command:

```
bin/rpsec spec/models/article_spec.rb:X
``` 
Where X is the number line of the specific test

[rspec-codeschool]:   https://www.codeschool.com/courses/testing-with-rspec
