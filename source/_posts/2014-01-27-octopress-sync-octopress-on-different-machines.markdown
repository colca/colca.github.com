---
layout: post
title: "Octopress: sync octopress on different machines"
date: 2014-01-27 07:28
comments: true
categories: 
---
1.Follow normal setup process
``` 
git clone git://github.com/imathis/octopress.git octopress
cd octopress    # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).
ruby --version  # Should report Ruby 1.9.3
gem install bundler
bundle install
rake install
rake setup_github_pages
``` 

2.Pull latest updates from remote branch to sync new machine with latest octopress
```
cd octopress\
git pull origin source
cd _deploy
git pull origin master
```

Now you can use rake to deploy new blogs from another machine!
