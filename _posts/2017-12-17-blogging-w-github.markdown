---
title:  "Start a blog with Github-Pages"
date:   2017-12-17 11:30:23
categories: [Others]
tags: [git]
---
**Theme** : Jekyll-uno ([https://github.com/joshgerdes/jekyll-uno](#))

**Operating System** : Ubuntu 16.04 LTS

**Ruby version** : ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]

install ruby :`sudo apt-get install ruby`

**Bundler version** : 1.16.0

install bundler : `gem install bundler`

Install Ruby-Dev : `sudo apt-get install ruby-dev` (we will need it for ffi installation in future step)

Clone the theme to a local directory :

```shell
git clone https://github.com/joshgerdes/jekyll-uno.git

cd jekyll-uno/

bundle install
```


if "ffi" installation is failing , then

`sudo gem install ffi -v '1.9.18'`

if nokogiri 1.8.0 installation is failing, then

```shell
sudo apt-get update

sudo apt-get install libxml2-dev

sudo apt-get install libxslt-dev

sudo apt-get install zlib1g-dev

sudo gem install nokogiri -- --use-system-libraries
```

When "**bundle install**" will be successful, 

![](https://i.imgur.com/DsWiLyL.png)

Then,

`bundle exec jekyll serve --watch`

and open

[http://127.0.0.1:4000/jekyll-uno/](#)

To stop the local server, do ctrl+C

Now, let's push the code to github.

Go to github.com and make a repository named your-username.github.io

In your local, change jekyll-uno directory to any directory name of your choice, let's say it as blogs-github

`cd blogs-github`

Modified site-settings in _config.yml

```yaml
# Site settings

title: <ur title>

description: '<some description>'

url: 'https://<your-username>.github.io/'

baseurl: ''

# google_analytics: 'UA-XXXXXX-X'

# disqus_shortname: 'your-disqus-name'

author:

name: 'Your name'

email: email-id

twitter_username: twitter-handle

facebook_username: ur-facebook-id

github_username: github-username

linkedin_username: linkedin-id
```

Then, it's time to push your changes to github, but to your repository

```shell
rm .git

git init

git add .

git commit -m "first commit"

git remote add origin https://<your-username>.github.io

git push origin master
```

Go to https://your-username.github.io and check your page

**Bonus Notes :**

1. To check dependencies used by Github Pages , check this (https://pages.github.com/versions/)

2. If jekyll build is not building your pages/Articles, then add below in your _config.yml

   `future: true`

   or do,

   `jekyll serve --future`

   This problems happens with Jekyll 3.x version, not with 2.x