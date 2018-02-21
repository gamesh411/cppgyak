---
layout: post
title: "Harmadik pluszminusz"
description: "Az második megíratott pluszminusz eredménye."
tags: [szamonkeres, cpp]
---
A következő kód a C++ öröklődést szemlélteti.
Mit gondolsz mi lesz a kimenet?
(milyen logika mentén jutottál a megoldáshoz?)

{% highlight cpp %}
#include <iostream>

class A {
public:
  virtual void print(int a) const {
  	std::cout << "A::f(int) said: "
  			  << a
  			  << std::endl;
  }
};
class B: public A {
public:
  void print(int a) const {
  	std::cout << "B::f(int) said: "
  			  << a
  			  << std::endl;
  }
};

int main() {
	B* bInstance = new B();
	A* basePtr = bInstance;
	basePtr->print(12);
	delete bInstance;
}

// A helyes válasz: Lefordul, a virtual kulcsszó miatt a futásideji típus fogja meghatározni a meghívott metódus implementációját, tehát a kiírt eredmény: "B::f(int) said: 12\n".
{% endhighlight %}
