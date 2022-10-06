+++
title = "List, ArrayList, LinkedList"
author = ["Babin Ion"]
date = 2021-01-01T00:00:00+02:00
tags = ["programming", "java", "tutorial"]
draft = false
+++

## ATENȚIE {#atenție}

Informația de aici este doar ceea ce îmi imaginez eu în cap când mă gândesc la acest subiect. **E posibil ca 90% din ceea ce am scris mai jos să fie greșit.** Scopul acestei pagini este de a oferi o prezentare generală, foarte abstractă și pe scurt a informației.


## Cio za ArrayList? {#cio-za-arraylist}


### Implementarea {#implementarea}

`List` - este interfața. `ArrayList` și `LinkedList` o implementează (`implements`).

`ArrayList` - o listă; un tablou obișnuit; același lucru ca un vector (nu confundați cu clasa `Vector` din Java. Astea două sunt [aproape](https://stackoverflow.com/questions/1386275/why-is-java-vector-and-stack-class-considered-obsolete-or-deprecated?noredirect=1&lq=1) aceleași, doar că listele sunt _unsynchronized_). Practic, la creare se alocă o bucată de memorie, necesară pentru un număr anumit de elemente. Dacă se adaugă mai multe elemente - o bucată încă mai mare se alocă, iar elementele din bucata precedentă (plină) sunt copiate în bucata nouă. Atunci un element este șters, elementele de după elementul șters trebuiesc recopiate. Recopierea folosește multă memorie.

`LinkedList` - listă (dublu)înlănțuită. La creare nu se alocă memorie. Când se adaugă un element nou - se crează un pointer care îl leagă de elementul precedent și de cel următor. De obicei, asta va folosi mai multă memorie decât un `ArrayList`. Atunci când un element este șters, singurul lucru care se schimbă este legătura (pointerul elementului dinaintea celui șters va fi legat de cel de după cel șters).


### Ce și când folosim? {#ce-și-când-folosim}

Folosim `ArrayList` când vrem să **stocăm** (salvăm, memorăm, citim și după accesăm etc.) date.

Folosim `LinkedList` când vrem să **manipulăm** datele (adăugăm, ștergem, modificăm). Keep in mind că această regulă nu trebuie dusă la absurd (nu folosi `LinkedList` când ai de șters 3-4 elemente, diferența va fi minimală, iar cel mai probabil un `ArrayList` va fi mai eficient).


### Array static vs Lista {#array-static-vs-lista}

Dacă știm dinainte cât de mare va fi array-ul dinainte (capacitatea sa maximă), folosim un array static (`type[] a = new type[n]`); (cel puțin, așa ar spune regula)

Dacă nu - folosim o listă (`ArrayList`). Cu toate acestea, eu folosesc `ArrayList` în ambele cazuri, și vă recomand s-o faceți și voi. De ce?


#### Item 28: Prefer lists to arrays {#item-28-prefer-lists-to-arrays}

Conform _Effective Java by Joshua Bloch_, cod precum

```java
// Va esua la runtime!
Long[] objectArray = new Long[1];
objectArray[0] = "I don't fit in"; // ArrayStoreException: tipul array-ului este Long, dar memoram un String
```

este considerat corect (pentru că eșuează abia la runtime). Acest cod, însă

```java
// Nu se va compila
List<Long> ll = new ArrayList<Long>(); // tipuri incompatibile
ll.add("I don't fit in");
```

este considerat greșit. Desigur, el este greșit în ambele cazuri (nu poți pune un `String` într-un container de tip `Long`), dar cu un array afli despre asta după ce programul rulează (runtime), în timp ce cu un list - la compile time (după ce programul a fost compilat, deci înainte de a rula).

El (Joshua) menționează încă o diferență, ce ține de integrarea cu generici, dar nu o includ aici, pentru că încă nu știu prea multe despre ei. Dacă voi sunteți interesați, citiți cartea.


### Programare (aproape)funcțională și clasa `stream` {#programare--aproape--funcțională-și-clasa-stream}


#### Funcțiile lambda {#funcțiile-lambda}


#### Puțin despre programarea funcțională {#puțin-despre-programarea-funcțională}

În limbajele de programare funcțională, instrumentul de bază este (_ați ghicit_) funcția. În astfel de limbaje avem funcții care primesc funcții ca parametru, și returnează alte funcții. Java nu este un limbaj funcțional, și totuși ea (pe lângă multe alte limbaje populare astăzi) împrumută lucruri din programarea funcțională. Desigur, implementarea lor, sintaxa ș.a.m.d variază mult (sunt, până la urmă, două paradigme diferite), însă esența rămâne tot acolo (dacă îți încruntezi privirea, așa încât ochii tăi seamănă cu cei ai unui asian).

Ceea ce ne apropie de programarea funcțională este `stream`. Nu știu prea multe despre ea. Mulțumesc lui Dumnezeu că exista Intellij, care scrie 80% din cod pentru mine. Știu doar că este așa clasă sau metodă - `stream`, care face magia.

O funcție lambda e pur și simplu o funcție fără nume. Să presupunem că avem codul următor:

```java
public int sum(int a, int b) { return a + b; }
public void afiseazaSumaDouaNumere() {
    System.out.println(sum(3, 5));
}
```

Desigur, nimeni treaz la cap nu scrie astfel de cod (dacă vrei să afișezi cât e 3 + 5 afișezi cât e 3 + 5), dar să ignorăm acest lucru pentru moment. Să presupunem că avem o astfel de funcție, care este folosită de o altă funcție, și numai o dată. `sum` este folosită doar de metoda `afiseazaSumaDouaNumere`, în interiorul ei, și nicăieri altundeva. Ei bine, am putea face ceva ca:

```java
public void afiseazaSumaDouaNumere() {
    System.out.println((3, 5) -> {return 3 + 5;}); // ia ca parametri doua numere oricare, returneaza suma
}
```

Codul de mai sus, chiar dacă nu funcționează din punct de vedere al limbajului, are sens, și exprimă ceea ce vrem să facem. Nu e nevoie să definești o funcție care face ceva simplu, și pe care oricum o vei folosi o singură dată. **Programatorii, la fel ca matematicienii, sunt oameni lenoși.** De ce să definești o funcție, când poți să nu o definești?


#### Iteratorii {#iteratorii}

Este destul de greu de explicat ce sunt iteratorii cu cuvinte, așa că o s-o fac prin exemple. Ceea ce trebuie să țineți minte e că fiecare iterator ia ca parametri cel puțin 2 lucruri: o funcție și o colecție (o listă), asupra căreia să aplice funcția. În Java, însă, acestea iau un singur lucru (ca parametru, în orice caz), și anume funcția. Colecția este specificată deodată, atunci când folosim metoda `.stream()` pe ea.

<!--list-separator-->

-  `map`

    `map` ia ca parametru o funcție, pe care o aplică fiecărui element al colecției. Rezultatul iteratorului `map` este o colecție, în care fiecare element este rezultatul aplicării funcției pe elementul colecției. În exemplul de mai jos, vom crea un nou `ArrayList`, în care fiecare element este elementul respectiv din `numere` + 1.

    ```java
    ArrayList<Integer> numere = new ArrayList<>(List.of(1,2,5,9,11,4,7,2));

    ArrayList<Integer> numereNou = (ArrayList<Integer>) numere.stream().map(element -> element = element + 1).collect(Collectors.toList());
    ```

    Destul de greu de citit, nu-i așa? De asta, vă recomand ca atunci când folosiți cel puțin două metode ca `.numeMetoda()` în stream-ul vostru, să le separați pe linii.

    ```java
    ArrayList<Integer> numereNou =
                    (ArrayList<Integer>) numere.stream()           // (1)
                            .map(element -> element = element + 1) // (2)
                            .collect(Collectors.toList());         // (3)
    ```

    Și acum, hai să analizăm pe linii.

    1.  `(ArrayList<Integer>)` - asta se numește _cast_, și reprezintă o transformare, dintr-un tip în altul. E nevoie să _cast_-uim, pentru că în linia 3, metoda `.collect(Collectors.toList())` nu specifica tipul listei.
        `numere.stream()` - sintaxa de folosire. Asta e ceea ce transformă obiectul nostru într-un obiect ciotkii 😎.
    2.  `.map(element -> element = element + 1)` - Apelul funcției `.map()` cu o lambda, care ia un argument și îi adaugă 1. Această funcție va crea o listă nouă, în care fiecare element este elemenmtul din lista `numere` + 1. O să accentuez faptul că `.map(...)` **nu modifică lista numere, ci creează o listă nouă**.
    3.  `.collect(Collectors.toList())` - ceea ce transformă rezultatul aplicării `map`-ului într-o listă. Nu știu prea bine cum funcționează, dar presupun că `map` nu crează obiectul propriu-zis, ci îl păstrează în memorie, iar `.collect()` îl ia din memorie și îl transformă în valoare.
