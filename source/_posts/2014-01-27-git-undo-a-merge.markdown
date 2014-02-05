---
layout: post
title: "git:revert"
date: 2014-01-27 07:22
comments: true
categories: 
---

To view git commit info: </br>
```
git log
```
press q to exit log navigation </br>
</br>
Reset using commit-sha or numbers: </br>
```
git reset --hard commit_sha

git reset --hard HEAD~5
```


Use gitk for details of commit info: </br>
```
gitk HEAD @{u}

gitk
```

from: </br>
http://stackoverflow.com/questions/2389361/undo-a-git-merge
http://stackoverflow.com/questions/12650261/git-says-local-branch-is-behind-remote-branch-but-its-not
