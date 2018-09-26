---
layout: post
title: Making Drupal Development Awesome
permalink: /article/making-drupal-development-awesome
tags:
    - drupal
    - drush
    - acquia dev desktop
    - phpstorm
---

Drupal is an awesome piece of software. Some might say that Drupal is difficult to learn, unnecessarily complicated, blah blah blah. Well, we Drupal developers also have lots of tools that helps make things easier for us. I'll be discussing 3 of them in this article.

1. **Acquia Dev Desktop**
2. **Drush**
3. **Drupal Module for PhpStorm**

**1) Acquia Dev Desktop**  
This is DAMP (Drupal + Apache + MySql + PHP). It comes with 5 PHP versions including 7.00 RC. Acquia Dev Desktop also comes with Drush 8.0.0 RC2. It makes it very much easier to set up a Drupal website, and also makes it easier to play with such advanced features of Drupal like the Multisites feature. It simplifies website deployment on the Acquia Cloud platform, and you can interacting with your remote Acquia Cloud website through a SSH utility that comes packaged with it.

**2) Drush**  
This is the coolest tool for Drupal which I've seen so far. **Drush** is short for _DRUpal SHell_; needless to say it's a command line utility that performs many useful Drupal tasks. Downloading and enabling Drupal modules, rebuilding registries, flushing caches, creating SQL dumps, running cron, viewing the status of your site are just some of the really awesome stuffs you can do with Drush.  
Before Drush, downloading a module (say Views) used to be like this:

* Visit Drupal.org
* Search for views
* Download the tarball or zip file from the project page
* Visit the /admin/modules page of your drupal site
* Import the module
* Enable the module

  
With Drush, it's like this:

* Navigate to your root directory on the command line
* Enter 'drush en ', in our case 'drush en views'
* After that, you'll only have to enter 'y' twice.

**3) Drupal Module for PhpStorm**

You're probably familiar with PhpStorm, which is arguably the most popular IDE among Drupal developers. PhpStorm comes with a Drupal module which provides Drupal-specific features for Drupal 6, 7 and 8. PhpStorm also comes with Command Line Tool Support which means you can integrate Drush within PhpStorm and get to use Drush without leaving PhpStorm. Then, you'll truly feel like you're in a storm.
