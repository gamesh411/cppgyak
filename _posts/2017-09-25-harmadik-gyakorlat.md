---
layout: post
title: "Input/Output másolás"
description: "Az input átirányítás összehasonlítása C-ben és C++-ban."
tags: [c, cpp]
---
Másoljuk a standard input-ot a standard outputra! (Vagyis készítsük el a linux-os cat parancs, vagy a windows-os copycon egy nagyon leegyszerűsített válotzatát)
A feladatot megoldó egyik lehetőség az hogy olvassuk addig a std inputot ameddig eof speciális értéket nem olvasunk, és írjuk ki a kimenetre.

{% highlight c %}
/* Stdio -> Stdout másoló C-ben */
#include <stdio.h>

int main() {
  char ch;
  while ((ch = getchar()) != EOF) {
    /* Fontos részlet, hogy a fenti értékadás egy olyan nyelvi elem, amely nem
       csak utasításként, hanem kifejezésként is funkcionál. A ch = getchar()
       eredménye, hogy a standard inputról egy karaktert beolvasunk, és
       eltároljuk a ch változóban. Ennek az értékadásnak mint kifejezésnek az
       értéke pedig a beolvasott karakter maga, és ezt hasonlítjuk össze az if
       elágazási feltétel kiértékelése során a speciális (alapból egy makróval
       definiált) EOF értékkel. Megjegyezhetjük, hogy itt az értékadás körüli
       zárójel nem opcionális, ugyanis az értékadás kisebb precedenciával bír
       mint logikai != összehasonlítás művelete, a zárójel nélkül nem azt
       kapnánk amit szeretnénk. Ebben az esetben a getchar() eredményét (a
       beolvasott karaktert) összehasonlítanánk az EOF-fal, majd az így kapott
       logikai érték konvertálódna át char típusúvá (implicit standard konverzió
       segítségével), szerencsés esetben warning kiséretében, szerencsétlen
       esetben pedig anélkül. A ciklus is teljesen más értelmet nyer ebben az
       esetben. */
    putchar(ch);
  }
  return 0;
}
{% endhighlight %}


{% highlight cpp %}
/* Stdio -> Stdout másoló C++-ban */
#include <iostream>

int main() {
  char ch;
  std::cin >> std::noskipws;
  // A standard input (std::cin) beolvasóoperátorának ( >> ) az
  // alapértelmezetett működése, hogy figyelmen kívül hagyja (átugorja) a
  // whitespace jellegű karaktereket. Ez teszi lehetővé, hogy mondjuk egy 3
  // egész számot tartalmazó sort (szóközzel elválasztva) a következő egyszerű
  // módon fel tudjuk dolgozni: int a, b, c; std::cin >> a >> b >> c;
  // ---
  // Az std::noskipws egy speciális, a standard könyvtárban definiált elem,
  // amely megváltoztatja annak az input stream-nek a működését, amellyel együtt
  // szerepel egy beolvasás operátoron belül ( >> ). A hatása az, hogy nem
  // hagyja figyelmen kívül ezek után a beolvasás a szóközjellegű karaktereket
  // sem. Ez most azért kell nekünk, hogy a sztenderd input lemásolása során ne
  // veszítsük el a szóközöket, tabulátorokat, újsor karaktereket stb. sem.
  while (std::cin >> ch) {
    // Fontos megjegyzést, hogy beolvasás operátorának mint kifejezésnek az
    // értéke egy logikai értékké konvertálódó érték, amely akkor igaz ha
    // beolvasásra használt stream állapota megfelelő, jó (vagyis lehet még
    // olvasni róla továbbra is), és akkor hamis ha valami miatt nem jó (vagy
    // valami hiba történt, vagy egész egyszerűen elértük a folyam végét, ami
    // most egyenértékű az EOF karakter olvasasásával).
    std::cout << ch;
  }
}
{% endhighlight %}
