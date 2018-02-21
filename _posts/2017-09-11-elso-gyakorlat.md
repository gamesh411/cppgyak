---
layout: post
title: "Hello World programok"
description: "A Hello World program megvalósítása C-ben és C++-ban."
tags: [c, cpp]
---
{% highlight c %}
/* Hello World C-ben */
#include <stdio.h>

int main() {
  printf("Hello World!\n");
  return 0;
}
{% endhighlight %}

{% highlight cpp %}
// Hello World C++-ban
#include <iostream>

int main() {
  std::cout << "Hello World!" << std::endl;
  // speciálisan a main esetében implicit módon 0 a visszatérési érték ha mást
  // nem mondunk
}
{% endhighlight %}
