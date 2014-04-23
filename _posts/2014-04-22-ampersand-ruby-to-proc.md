---
layout: post
title:  "To Proc: &"
date:   2014-04-22 09:00:00
categories: jekyll update
---
When I write code, I sometimes find myself doing something that I don't really understand. A lot of times, those things tend to be language constructs that I somewhat take for granted. I think we all can be guilty of this sometimes. It's not necessarilly bad, but understanding it is certainly better than not. 

One thing that I use all the time, but didn't question until recently is below.

{% highlight ruby %}
  strings = ["hello", "my", "name", "is", "joe"]

  upcase_strings = strings.map(&:upcase) # ["HELLO", "MY", "NAME", "IS", "JOE"]
{% endhighlight %} 

What is that ampersand (&) in my call to `map` doing for me? I had no idea up until I was driven to ask a friend that knew Ruby much better than I did. The ampersand is actually shorthand for calling `to_proc` on a symbol. But how does that work? 

One thing we have to remember is that `map` actually takes a code block. Since that is the case,   passing it a proc object will actually work. The following code is equivalent to the above:

{% highlight ruby %}
  upcase_strings = strings.map {|string| string.upcase} # ["HELLO", "MY", "NAME", "IS", "JOE"]
{% endhighlight %} 

`to_proc` is smart enough here to understand that we want to call the method we're *procifying* on the object that is being passed to the block. `map` is simply passing each string literal object to the code block as it iterates through the collection. 

It's nice to understand things that I did out of habit that I didn't truly understand before. 

