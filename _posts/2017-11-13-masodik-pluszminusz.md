---
layout: post
title: "Második pluszminusz"
description: "Az második megíratott pluszminusz eredménye."
tags: [szamonkeres, cpp]
---
Lefordul-e a követekező C++ kód?
Ha igen, mi történik? Mit ír ki?

{% highlight cpp %}
#include <iostream>

struct A {
  int member;
  A(): member(42) {
    new A(117);
    std::cout << "a";
  }
  A(int param): member(param) {
    std::cout << "a";
  }
  ~A() { std::cout << "b" }
};
int main() { A instance; }

// A helyes válasz: Lefordul, az instance változó automatikus élettartamú, és a default konstruktor fut le először.
// Itt a member változó beállításra kerül 42-es értékre, majd egy dinamikus élettartamú névtelen példány jön létre a new kulcsszó hatására.
// Lefut ennek az egyparaméteres konstruktora, és ott kiírásra kerül az első "a" string. Ezt követően folytatódik a default konstruktor futása, és kiírásra kerül a második "a".
// Végül a main() végén lefut az automatikus élettartamú destruktora.

// A helyes végeredmény "aab".
{% endhighlight %}
