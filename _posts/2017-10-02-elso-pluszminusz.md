---
layout: post
title: "Első pluszminusz"
description: "Az első megíratott pluszminusz eredménye."
tags: [szamonkeres, cpp]
---
Lefordul-e a követekező C++ program?
Ha igen, mit ír ki?

{% highlight cpp %}
#include <iostream>

int main() {
	float a = 5 + 7./2;
	std::cout << (a/2);
}
// A helyes válasz: Lefordul, 4.25-t ír ki.
{% endhighlight %}
