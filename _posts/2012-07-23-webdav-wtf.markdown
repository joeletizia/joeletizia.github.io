---
layout: post
title:  "WebDAV Eats my PUTS and POST Requests"
date:   2012-07-23 09:00:00
categories: jekyll update
---

I work on a project that implements a [RESTful webAPI][rest-wiki] that allows users to interact with the system over HTTP to get data and update things...nothing special.

When getting my environment set up, I had a problem sending the API POSTs and DELETEs. I would always be returned a [405][405-wiki]. Well, the short of it came down to the WebDAV module that IIS 7 had running. I uninstalled that and it worked like a charm; my requests are now going through successfully.

[This][help] is the article that helped me out.

Total wtf?? moment.

[rest-wiki]: http://en.wikipedia.org/wiki/Representational_state_transfer
[405-wiki]: http://en.wikipedia.org/wiki/405_(HTTP_status_code)#405
[help]: http://forums.iis.net/t/1163441.aspx
