---
layout: post
title:  "Upgrading sunspot-rails 1.3.3 to 2.1.1"
author: Luis Mancilla Ávila
date:   2015-01-08 18:00
categories: sunspot solr rails
---

Sunspot-rails gem is an interface between Solr indexing service and Rails. Here you will learn how to upgrade without fear.

<!-- more -->

Step 1
----------------------------

Remove **./solr** directory.

Step 2
----------------------------

Start a solr instance by running **sunspot:solr:run** task. This will create a **new** ./solr directory.

Step 3
----------------------------

Quit sunspot:solr:run task and then execute **sunspot:solr:start** as normally you would do.

