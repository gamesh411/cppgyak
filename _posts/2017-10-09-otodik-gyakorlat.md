---
layout: post
title: "Input/Output másolás, STL"
description: "Az input átirányítás C++-ban STL segítségével."
tags: [cpp]
---
A Standard Template Library (STL) került előtérbe, megismerkedtünk pár container-rel, illetve algoritmussal, és az iterátorok fogalmával.

{% highlight cpp %}
// Copy algoritmus bevezetése
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
  std::vector<int> a(5); // 5 elemű, default inicializált intek vektora
  std::vector<int> b(3, 42); // 3 db 42-es értéket tartalmazó vektor
  
  std::copy(b.begin(), b.end(), a.begin());
  
  for (int i = 0; i < a.size(); ++i) {
    std::cout << a[i] << " ";
  }
}

// Kiírt eredmény: 42 42 42 0 0
{% endhighlight %}

Ezzel a tudással felvértezve kis magyarázattal érthetővé válik a következő példa:

{% highlight cpp %}
// Input átmásolása Outputra
#include <algorithm>
#include <iostream>
#include <iterator>

int main() {
  std::copy(std::istream_iterator<char>(std::cin),
            std::istream_iterator<char>(),
            std::ostream_iterator<char>(std::cout));
}

// Az std névtérben levő istream_iterator, és ostream_iterator input- és
// outputfolyamok iterátora tud lenni. A szintén std névtérben deklarált copy
// algoritmus 3 paraméterrel rendelkezik, a másolandó elemek kezdetét jelölő az
// első, amellyel a cin standard inputra szeretnénk mutatni. Konceptuálisan az
// elejére, lévén hogy honnan kezdenénk a másolást ha nem az elejéről. Az
// istream_iterator<char>(std::cin) valójában egy olyan konstruktor, amely ha
// kap paraméterként egy istream típusú folyamot, akkor a "kezdetére mutató"
// iteratort ad nekünk. Ha paraméter nélkül hívjuk, akkor pedig az "input végét
// jelző" iteratort ad vissza. Ez a copy algoritmus 2. paramétere. Az
// istream-mel teljesen analóg az ostream esete (3. paraméter), itt csak a
// kezdet kell. A fenti kóddal szemantikáját tekintve ekvivalens, bár nem műdödő
// kódrészlet lehetne ezek alapján a következő:
// std::copy(std::cin.begin<char>(),
//           std::cin.end<char>(),
//           std::cout.begin<char>());
{% endhighlight %}

Ha teljesen korrektek akarunk lenni, akkor a fenti példa még nem ugyanazt produkálja mint az őt megelőző megoldások. Ugyanis a fehér-szóközök (whitespace) kezelése úgy történik, hogy figyelmen kívül hagyja őket az input oldal. Így lehet megjavítani a legegyszerűbben:

{% highlight cpp %}
// Input átmásolása Outputra whitespace különleges kezelése nélkül
#include <algorithm>
#include <iostream>
#include <iterator>

int main() {
  std::copy(std::istream_iterator<char>(std::cin >> std::noskipws),
            std::istream_iterator<char>(),
            std::ostream_iterator<char>(std::cout));
}
{% endhighlight %}
