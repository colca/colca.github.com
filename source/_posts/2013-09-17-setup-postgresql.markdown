---
layout: post
title: "Setup Postgresql for Rails Project"
date: 2013-09-17 18:35
comments: true
categories: RubyOnRails 
---
1. Install postgresql <br>
```
apt-get install postgresql
```
update Gemfile, eliminate sqlite, add pg gem
```
gem 'pg', '0.12.2'
```
then bundle install,<br>
solution if error encounted with pg gem install<br>
http://about-opensource.blogspot.com/2012/09/rails-3-application-issue-you-need-to.html

2. Manage database<br>
To enter psql command with default user postgres<br>
```
psql postgres
```

To create database, list database, list roles<br>
```
CREATE USER yourname WITH SUPERUSER PASSWORD 'yourpassword';
\l
\du
```

To quit<br>
```
\q
```
3. Connect in Ruby, edit database.yml<br>
```
  adapter:  postgresql
  database: postgres	  
  pool: 5
  username: username
  password: password
```
links<br>
Heroku postgresql:
https://devcenter.heroku.com/articles/heroku-postgresql<br>
Postgresql document:
http://www.postgresql.org/docs/9.2/static/bookindex.html


