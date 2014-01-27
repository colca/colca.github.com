---
layout: post
title: "Setup Piwik for Octopress Analytics"
date: 2013-09-20 19:00
comments: true
categories: 
---

1. Install Piwik on Ubunto 12.04 Cloud Server</br>
Instruction Details: https://www.digitalocean.com/community/articles/how-to-install-piwik-on-an-ubuntu-12-04-cloud-server </br>

2. Add Piwik Script to Each Octopress Blog</br>

Edit your source/_includes/custom/after_footer.html, add below script
```
<!-- Piwik -->
<script type="text/javascript"> 
  var _paq = _paq || [];
  _paq.push(['trackPageView']);
  _paq.push(['enableLinkTracking']);
  (function() {
    var u=(("https:" == document.location.protocol) ? "https" : "http") + "://127.0.0.1/piwik//";
    _paq.push(['setTrackerUrl', u+'piwik.php']);
    _paq.push(['setSiteId', 1]);
    var d=document, g=d.createElement('script'), s=d.getElementsByTagName('script')[0]; g.type='text/javascript';
    g.defer=true; g.async=true; g.src=u+'piwik.js'; s.parentNode.insertBefore(g,s);
  })();

</script>
<noscript><p><img src="http://127.0.0.1/piwik/piwik.php?idsite=1" style="border:0" alt="" /></p></noscript>
<!-- End Piwik Code -->
```    
reforence: http://octopress.org/docs/theme/template/ </br>
//github.com/WyriHaximus/OctopressPiwik </br>

3. Login to Piwik to view the realtime analytics
http://127.0.0.1/piwik/index.php



 
