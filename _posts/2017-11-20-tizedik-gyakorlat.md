---
layout: post
title: "Altípusos polimorfizmus"
description: "Példa ami szemlélteti a dinamikus és statikus típus alapján való metódushívásokat."
tags: [cpp]
---
A C++-ban alapértelmezetten minden metódushívás a forráskód alapján kikövetkeztethető módon történik, ennek a neve: statikus üzenetküldés (static dispatch). Lehetőség van azonban a dinamikus üzenetküldésre is, ezt a `virtual` kulcsszó jelzi.

{% highlight cpp %}
// Worker.h
#ifndef WORKER__H
#define WORKER__H

class Worker {
    virtual void fetch();
    void work();
    void store();
public:
    void doWork();
};

#endif
{% endhighlight %}

{% highlight cpp %}
// Worker.cc
#include "Worker.h"

#include <iostream>

void Worker::fetch() { std::cout << "Worker::fetch()" << std::endl; }
void Worker::work() { std::cout << "Worker::work()" << std::endl; }
void Worker::store() { std::cout << "Worker::store()" << std::endl; }
void Worker::doWork() {
    fetch();
    work();
    store();
}
{% endhighlight %}

{% highlight cpp %}
// SVNWorker.h
#ifndef SVN_WORKER__H
#define SVN_WORKER__H

// Tudnia kell a compilernek, hogy létezik a Worker osztály.
#include "Worker.h"

class SVNWorker: Worker {
    // Overriding the fetch method of Worker
    void fetch();
};

#endif
{% endhighlight %}

{% highlight cpp %}
// SVNWorker.cc
#include "SVNWorker.h"

#include <iostream>

void SVNWorker::fetch() { std::cout <<  "SVNWorker::fetch()" << std::endl; }
{% endhighlight %}

{% highlight cpp %}
// GitWorker.h
#ifndef GIT_WORKER__H
#define GIT_WORKER__H

// Tudnia kell a compilernek, hogy létezik a Worker osztály.
#include "Worker.h"

class GitWorker: public Worker {
    // Overriding the fetch method of Worker
    void fetch();
};

#endif
{% endhighlight %}

{% highlight cpp %}
// GitWorker.cc
#include "GitWorker.h"

#include <iostream>

void GitWorker::fetch() { std::cout <<  "GitWorker::fetch()" << std::endl; }
{% endhighlight %}

{% highlight cpp %}
#include "GitWorker.h"
#include "SVNWorker.h"

#include <iostream>

void callDoWork( Worker & w ) {
    w.doWork();
}

int main() {
   GitWorker gw;
   SVNWorker sw;

   callDoWork(gw);
   // output:
   // GitWorker::fetch()
   // Worker::work()
   // Worker::store()
   callDoWork(sw);
   // output:
   // SVNWorker::fetch()
   // Worker::work()
   // Worker::store()
}
{% endhighlight %}

A fenti felosztás szemlélteti, hogy a projektet úgy is össze lehet állítani, hogy különböző fordítási egységekre bontjuk. Az egyes elemek object file-ra való fordítása után a linker (szerkesztő) létrehozza a futtatható állományt.

{% highlight bash %}
A fordító a -c kapcsolóval a tárgykód elkészítésekor megáll, és a forrás nevével
megegyező, de .o kiterjesztésű fájlban tárolja az eredményt.

Ezeket fel lehet használni a futtható fájl elkélszítésekor, és hozzálinkelődnek
a készülő állományhoz. (Elképzelhető hogy egy fordítási paracs csak ilyen object
fájlokból áll, ilyenkor csak linkelés történik. De lehet hogy egy forrásfájlt és
object fájlokat adunk meg egymás mellett, ekkor a forrásfájlokat lefordítja,
és összelinkeli a belőle készült object file-t a többi object file-lal.)
Most az utóbbira láthatunk példát:
minta:

c++ -c Worker.cc
c++ -c GitWorker.cc
c++ -c SVNWorker.cc
c++ main.cc Worker.o GitWorker.o SVNWorker.o -o main
./main

egysorosan leírva:
c++ -c Worker.cc GitWorker.cc SVNWorker.cc && c++ main.cc *.o -o main && ./main
{% endhighlight %}
