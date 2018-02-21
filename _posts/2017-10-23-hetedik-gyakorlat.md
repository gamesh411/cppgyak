---
layout: post
title: "Funktorok"
description: "Egy konkrét feladat megoldása a gömbölyű zárójel operátor definiálásával."
tags: [cpp]
---
Tekintsük a követekező feladatot:
Írjunk programot, amely a standard inputról olvas be karakterrenként, és a standard kimenetre írja az eredményt.
A bemenet feldolgozása a következőképpen zajlik:
Ha nem speciális karaktert olvasunk akkor azt egy eredménylistába helyezzük (2 kitűntetett speciális karakter létezik a '[' és ']').
Ha '[' jelet olvasunk, akkor onnantól kezdve az eddig összeállított eredménylista elejétől folytatjuk az összeállítást.
Ha ']' jelet olvasunk, akkor onnantól kezdve pedig az eredménylista végére füzzük a karaktereket.
Hasonlóan, mintha egy hibás szövegszerkesztő kimenetét szeretnénk kijavítani, amelyben az ugorj a sor eljére (home) művelet csak egy '[' jelet hagy az outputban. Hasonlóan ehhez az ugorj a sor végére (end) művelet pedig egy ']' jelet hagy a kimenetben. Szeretnénk a helyes kimenetet előállítani.
Pl.:
input: World[Hello ]!
output: Hello World!

Feltételezve, hogy a következő forrásfájlt corrector.cpp néven tároljuk, tesztelhetjük a követekező parancssal (unix-alapú környezetben):

{% highlight cpp %}
c++ corrector.cpp -o corrector && echo 'Hello[World ]!' | ./corrector
{% endhighlight %}


{% highlight cpp %}
#include <algorithm>
#include <iostream>
#include <iterator>
#include <list>

// Generic printing functor.
//
// Típus-paraméterezett nyomtató funktor.
class Print {
public:
  template <typename T> void operator()(const T &val) { std::cout << val; }
};

// The input is copied to the output, character by character, but when '[' is
// encountered the cursor that inserts the copies into the result should jump to
// the beginning of the list (as if a home button were pressed inside a text
// editor), and in case a ']' is found, the cursor should jump back to the end
// of the list being created.
//
// A bemenet karakterenként átkerüla a kimenetre, de amikor '[' karakterrel
// találkozunk, akkor kurzor, ami beilleszti a másolatokat az eredménylistába,
// a lista elejére ugrik (mintha a home gombot nyomta volna meg valaki egy
// szövegszerkesztőn belül), és ha ']' karaktert látunk, akkor a kurzor
// visszaugrik az éppen készülő lista végére.
class Correct {
  std::list<char> result;
  std::list<char>::iterator it;

public:
  Correct() : it(result.begin()) {}
  void operator()(char nextInput) {
    switch (nextInput) {
    case '[':
      it = result.begin();
      break;
    case ']':
      it = result.end();
      break;
    default:
      result.insert(it, nextInput);
    }
  }
  std::list<char> getResult() const { return result; }
};

int main() {
  // Read characters from standard input to a list.
  //
  // Egy listába olvassuk a karaktereket a bementről.
  std::istreambuf_iterator<char> first(std::cin), last;
  std::list<char> input(first, last);

  // Apply for_each algorithm to the input with a functor, that copies the input
  // to an other list (with some modifications).
  //  
  // A for_each algoritmust alkalmazzuk a bemenetre egy olyan funktorral, ami
  // módosításokkal élve ugyan, de átmásolja a bementi listát egy másikba.
  std::list<char> endResult =
      std::for_each(input.begin(), input.end(), Correct()).getResult();

  // Write the modified list to the standard output.
  //
  // A módosított listát kiírjuk a kimenetre.
  std::ostream_iterator<char> stdout(std::cout);
  std::copy(endResult.begin(), endResult.end(), stdout);
}
{% endhighlight %}
