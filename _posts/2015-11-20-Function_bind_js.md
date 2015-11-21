---
layout: post
title: "Function.bind in JavaScript"
description: ""
category:
tags: []
---
{% include JB/setup %}

Sometimes in JavaScript, we need a way of binding a Function to the correct scope of ‘this’ when applying the Function to a given context. Enter Function.prototype.bind, implemented thus:

{% highlight js %}
Function.prototype.bind = function(context) {
  var fn = this;
  var args = Array.prototype.slice.call(arguments, 1)
  return function(){
    return fn.apply(context, args.concat([].slice.call(arguments)));
  };
};
{% endhighlight %}

When using bind, we’re looking to bind a Function to the supplied context. However, the tricky part is, we may have many more additional arguments passed into the original Function, as well as additional arguments that are passed into the functions included as additional arguments. Confused? Let me try to explain.

Inside of bind, we set ‘this’ to a variable which allows us to use ‘this’ in its proper context of the function that’s calling it: the Function to the left of the bind call. In order to access any additional arguments that are passed into the function beyond the given ‘context’, we need to copy them into an array by using the Array.prototype.slice.call on the additional arguments beyond ‘context’. This will allow us to be able to access them and apply them to the ‘context’ as well.

Bind will return a function that applies all arguments onto the supplied ‘context’. This is where it gets tricky. The function that it returns may have ADDITIONAL arguments that are not implicitly stated. Therefore, once in the inner function, we need to concat all possible arguments onto our original arguments array from the containing function. We call Array.prototype.slice.call on the inner function arguments, concat them with the outer function additional arguments, and then we apply them to the Function to the left of .bind. This ensures that all possible arguments are applied to the supplied ‘context’ in which we are trying to bind the Function to.
