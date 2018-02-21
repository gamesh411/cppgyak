---
layout: post
title: "A Fahrenheit-konverzió"
description: "A Fahrenheit-Celsius átváltás megvalósítása C-ben és C++-ban."
tags: [c, cpp]
---
A Fahrenheit konverziós feladat specifikációja:
Adjuk meg a [-60, 120] zárt intervallumon 10-es lépésközzel haladva a Fahrenheit-ben megadott hőmérséklet-értékeket Celsius fokban.

Az átváltás szöveges megoldása:

* A Fahrenheitben vett értékből vonjunk le 32-t!
* Az így kapott értéket osszuk el 9-cel!
* Majd az így kapottat szorozzuk meg 5-tel!

celsius (fahrenheit) = 5/9 * (fahrenheit - 32)

{% highlight c %}
/* Naiv megközelítés C-ben a Fahrenheit konverziós mintaprogramra */
#include <stdio.h>

int main() {
  int lower = -60;
  int upper = 120;
  int step = 10;
  int i = lower;
  /* A hetvenes évekbeli nézetekhez igazodva a blokk elején deklarálva van az
   * összes használt lokális változó */

  for (; i <= upper; i += step) {
    printf("%d Fahr\t%d Celsius", i, 5 / 9 * (i - 32));
    /* Ez az utolsó sor biztosan rossz, hiszen gondoljunk csak bele mit
       jelent a / művelet. Ez egész osztás, vagyis int, int
       -> int leképezést takar, és megadja hogy a bal oldali egész számban
       hányszor van meg a jobb oldali egész szám. Ennek az értéke csak egész
       szám lehet. Az 5/9 értéke 0, mert 5 és a 9  is egy-egy egész-literál, és
       mivel az 5 kisebb mint a 9, egyszer sincs meg a jobb oldal a bal
       odaliban.*/
  }

  return 0;
}
{% endhighlight %}

A 70-es években még ennyire sem lettek volna pazarlóak a C programozók. Kihasználták volna a preprocesszor nyújtotta lehetőségeket a következő példában látható módon:

{% highlight c %}
/* Hagyományos C-s megkövelítés a Fahrenheit konverziós mintaprogramra */
#include <stdio.h>

#define LOWER -60
#define UPPER 120
#define STEP 10

#define FAHR_TO_CELS(X) ((5. / 9) * ((X)-32))
/* Figyeljük meg, hogy az 5. egy lebegőpontos literál, vagyis a / művelet már
   nem egészosztást jelent, hanem a valós számok közötti osztás C-nyelv-beli
   operátora lesz. Hogy ez elvégezhető legyen itt megjelenik később, a kifejezés
   értelmezésekor, egy implicit típuskonverzió, amely 9 egész értékből egy valós
   értékű 9-est csinál. Ezután elvégezhető a művelet.
   A feltétlen szükségesnél több zárójel használata azért javasolt, mert a
szövegszerű behelyettesítés miatt nem feltétlen tudununk gondolatban felkészülni
minden eshetőségre, erre egy példa:
#define mult2(num) num * 2
int doubleOf3 = mult2(1 + 3);
Ez valójában a behelyettesítés után: int doubleOf3 = 1 + 3 * 2;
Ami 7, nem 6.

A zárójel használatával:
#define mult2(num) (num) * 2
int doubleOf3 = mult2(1 + 3); -> int doubleOf3 = (1 + 3) * 2;

Hasonló megfontolás miatt nem csak a paraméterek, hanem az egész kifejezés
zárójelezése is jó ötlet lehet.
*/

int main() {
  /* Nem foglaltak volna le memórát a stack-en még 3 integernek sem. A
   * preprocesszor használatával ezt meg tudták spórolni. */
  int i = LOWER;
  for (; i <= UPPER; i += STEP) {
    printf("%d Fahr\t%d Celsius", i,
           FAHR_TO_CELS(i)); /* Így már helyes eredményt kapunk. Fontos még
           megjegyezni, hogy ez függvényszerű preprocesszor-makró, és az eddigi
           lokális változókat helyettesítő makrók is mind szövegszerű
           behelyettesítéssel működnek, bemásolódik a makró szövege a hivatkozás
           helyére. Így minden nyelvi ellenőrzést és megszorítást figyelmen
           kívül hagy, és az esetleges hibák később kerülnek elő a fordítás
           folyamatában, illetve tőbb lehetőség adott arra, hogy hibás kódot
           írjunk, ami első pillantásra jónak tűnik. */
  }

  return 0;
}
{% endhighlight %}


C++-ban már több eszköz áll rendelkezésre, hogy ugyanilyen megfontolások mentén, a felmerülő megszorításokra típushelyes, nyelvi eszközökkel reagáljunk.

{% highlight cpp %}
// C++-beli megvalósítása a Fahrenheit konverziós feladatnak
#include <iostream>

inline double fahr_to_cels(double fahr) { return 5. / 9 * (fahr - 32); }
// Itt egy függvénybe szerveztük ki a konverziós logikát, amely a paraméterét
// típushelyesen kezeli, és még a teljesítménybeli megfontolásokra is
// tekintettel van az inline kulcsszónak köszönhetően az inline azt jelenti,
// hogy a fordítóprogram figyelmébe ajánljuk azt, hogy ezt a függvényt a
// kódoptimizációs fázisban "inline-osítsa", vagyis a függvény-t megvalósító
// bináris utasítássorozatot a hívás helyére kibontsa, megkerülve ezzel a
// függvényhívás többletköltségét. Ebben az esetben egyenértékű kódot kapunk,
// mintha szövegszerűen kifejtenénk egy makró segítségével, mégis megőrizzük a
// típushelyes paraméterkezelést, illetve a fordító még mindig dönthet úgy, hogy
// hatékonyabb, vagy jobb kódot tud bizonyos esetekben generálni ha nem
// inline-olja a függvényünket.

int main() {
  const int lower = -60;
  const int upper = 120;
  const int step = 10;
  // A const kulcsszó azt jelüli, hogy minősített változók értéke nem fog
  // változni. Ha mégis változást eredményező kódba ütközik a fordító, akkor
  // hibaüzettel jelzi a nem megengedett lépést. A fordító ilyenkor szabadabb
  // kezet kap az optimizációra, nem feltétlek kell a stack-en tartania ezeket a
  // változókat.

  for (int i = lower; i <= upper; i += step) {
    std::cout << i << "Fahr\t" << fahr_to_cels(i) << "Celsius" << std::endl;
  }
  // Megengedett, sőt ajánlott a változók deklarációját a lehető legkésőbbi
  // pontig elhalasztani a kódban (lásd int i). Ezzel csökkenthető a változók
  // élettartama és láthatósági köre is, amelyek mind segítik a fordítót a
  // potenciálisan hatékonyabb kódgenerálásban.
}
{% endhighlight %}
