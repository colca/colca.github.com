---
layout: post
title: "Ruby : rescue NoMethodError inside loop"
date: 2014-02-17 18:03
comments: true
categories: Ruby
---
```
while condition
 ...
 begin
 meth.call
 rescue NoMethodError 
 puts "NoMethodError is caught"
 next
 end
end
```

