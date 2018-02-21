---
layout: post
title: "Élettartam"
description: "A létrehozott objektumok élettartamát szemléltető példa."
tags: [cpp]
---
A következő példa szemlélteti egy osztály példányainak élettartamát.

A header file tartalma (LifetimeLogger.h):

{% highlight cpp %}
#ifndef LIFETIME_LOGGER__H
#define LIFETIME_LOGGER__H

#include <iostream>
#include <string>

class LifetimeLogger {
  std::string name;

public:
  // Nincs default constructor, mert már definiáltunk egy ctor-t.
  // Bár amit megadunk itt, az paraméteres, mégis megakadályozza, hogy a fordító
  // legenerálja az eredeti default ctor-t).
  LifetimeLogger(const std::string &);

  // Copy constructor.
  LifetimeLogger(const LifetimeLogger &);

  // Copy assignment operator.
  // A parmétert érték szerint veszzük át, hogy már rögtön készítsünk róla egy
  // másolatot. Lásd copy-and-swap idiom.
  LifetimeLogger &operator=(const LifetimeLogger);

  friend void swap(LifetimeLogger &, LifetimeLogger &);

  // Destructor.
  // A virtual kulcsszó azért kell, hogy ha esetleg valaki leszármaztat ebből az
  // osztályból, és ősosztály-pointer-en keresztül próbálja meg felszabadítani
  // akkor ha nincs az ősosztály destruktorán virtual minősítés, az nem várt
  // viselkedéshez vezet (undefined behaviour). Ha van, akkor minden destruktor
  // lefut szép sorban.
  virtual ~LifetimeLogger();
};

LifetimeLogger::LifetimeLogger(const std::string &name) : name(name) {
  std::cout << "Single param constructor, "
            << "name: " << name << ", "
            << "this: " << this << std::endl;
}

LifetimeLogger::LifetimeLogger(const LifetimeLogger &other) : name(other.name) {
  std::cout << "Copy contructor, "
            << "name: " << name << ", "
            << "this: " << this << std::endl;
}

LifetimeLogger &LifetimeLogger::operator=(LifetimeLogger other) {
  swap(*this, other);

  std::cout << "Copy assignment operator, "
            << "name: " << name << ", "
            << "this: " << this << std::endl;
}

void swap(LifetimeLogger &a, LifetimeLogger &b) {
  using std::swap;
  swap(a.name, b.name);
}

LifetimeLogger::~LifetimeLogger() {
  std::cout << "Destructor, "
            << "name: " << name << ", "
            << "this: " << this << std::endl;
}

#endif
{% endhighlight %}


Az implementációs file tartalma (lifetime.cc):
{% highlight cpp %}
#include "LifetimeLogger.h"

int main() {
  LifetimeLogger a("a");
  LifetimeLogger aCopy = a;
  LifetimeLogger c("c");
  aCopy = c;
}
{% endhighlight %}


És egy futás kimenete:
{% highlight bash %}
c++ lifetime.cc -o lifetime && ./lifetime
{% endhighlight %}


{% highlight bash %}
Single param constructor, name: a, this: 0x7ffff3d2a0f0
Copy contructor, name: a, this: 0x7ffff3d2a100
Single param constructor, name: c, this: 0x7ffff3d2a110
Copy contructor, name: c, this: 0x7ffff3d2a120
Copy assignment operator, name: c, this: 0x7ffff3d2a100
Destructor, name: a, this: 0x7ffff3d2a120
Destructor, name: c, this: 0x7ffff3d2a110
Destructor, name: c, this: 0x7ffff3d2a100
Destructor, name: a, this: 0x7ffff3d2a0f0
{% endhighlight %}
