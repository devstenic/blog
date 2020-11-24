---
template: post
title: "Prosty komunikator z użyciem Flutter i Firebase #1"
slug: prosty-komunikator-z-użyciem-flutter-i-Firebase-1
draft: false
date: 2020-04-20T20:40:31.840Z
description: "Prosty komunikator z użyciem Flutter i Firebase #1"
category: development
tags:
  - "#flutter #dart #firebase"
---
<div style="text-align: justify;">Krótka seria artykułów jak stworzyć prostą aplikację typu Messenger opartą o Firebase. Jak podłączyć Firebase do aplikacji Flutter i stworzyć nowy projekt macie tutaj. Oczywiście nie będziemy zagłębiać się w architekturę aplikacji i rozdzielenie logiki firebase z widokiem. Skupimy się tylko na wykorzystaniu oficjalnych pluginów firebase do budowy prostego komunikatora.

Na początku stworzymy bazę danych, która będzie przetrzymywała nasze kulturalne, inteligentne, o wysokim poziomie merytorycznym konwersacje. W tym celu udajemy się kursorem myszy w podróż do lewego górnego rogu okna na stronie głównej naszego projektu w firebase. Odnajdujemy tam zakładkę “Programowanie” i klikamy na Database, a następnie “Utwórz bazę danych”.</div>

![](https://lh6.googleusercontent.com/Mb5zk0xQ2A3Qhv7CID1TEOqnnBbiNa-7x4StkmUmpxBxwY-aMiDpYe7qiY_fOhA2Cg7wSEqb-MtRybBNEX7ZlahADh0EIivYlt0ZXxGy2tdOcOSpChFq6HCSqohcC74bIzF_lJl5)



<div style="text-align: justify;">Następnie w kolejnym oknie zaznaczamy “zacznij w trybie testowym” i klikamy dalej.</div>



![](https://lh6.googleusercontent.com/DIAc4_9_N9Vz9GIYoG3S0EShktLRsmzyFFCjvIvb621NeOa4gPJZHKA_jGFiQY1dAxl4NZU9-bechr-OyqTikg7bNgGu0gA66adeYbk4FryTt0plDrS607kviXAN3DViRxnWD0YZ)



<div style="text-align: justify;">Następnie wybieramy wiele regionów “eur3 (europe-west)”. Dlaczego? No bo jesteśmy w Europie, a to “eur” to chyba skrót od tego… a tak serio to o wyborze lokalizacji dla naszego projektu możecie poczytać tutaj: </div> 

<https://firebase.google.com/docs/projects/locations>

Klikamy gotowe i czekamy na udostępnienie Cloud Firestore i konfigurację bazy danych.



![](https://lh5.googleusercontent.com/mZqPTEkc65UfGJeeN_kuu6kJqOy0YdWz2KmG7oW1U8pmnSdyFKskH5e9g3_79YVobm1GDsYVvan9czhjplTNITwPgulEABMnPfhjH_eegi1aqZow2zrXQYpKw8OJz6kR_Rksyjke)



Dobrze jeśli wszystko poszło sprawnie możemy stworzyć teraz kolekcję i dodać jakieś przykładowe dane. Klikamy na “Utwórz kolekcję”.



![](https://lh5.googleusercontent.com/ZqZ_knBRsRpvppyS0c4WDgJUP27ULT0Iv90XQ5sEaaGMSQ8imWKyi6xB3oXqSIujfbXlbreFVMWO_THLZIhI278470J4POHpFEsjDcxMtevP2t-_kz7LnLPL8KW6l-hXPISdbCOi)



<div style="text-align: justify;">Nazwijmy naszą kolekcję pięknym dźwięcznym spolszczonym wyrażeniem “czaty”. Przejdźmy dalej. W następnym oknie wybieramy “Automatyczny identyfikator” i uwaga.. nie dodajemy żadnego pola. Usuwamy istniejące pole i zapisujemy.</div>



![](https://lh4.googleusercontent.com/lA4NmuSWnZ4sL8BeC4utCj3iMFwudHoybwlXC2YWP-gLO67IXXsB7tkA9S86cWsn3OQpp5ZWhanmyHO7A731InHHFI1KN95MHQj7yer-1X2W9V0DA8WIAFbv-mYIEmhMA3dpDeyx)



<div style="text-align: justify;">Dlaczego tak? W firestore mamy strukturę typu kolekcja -> dokument -> kolekcja -> dokument. Kolekcje zawierają dokumenty, a dokumenty mogą zawierać inne kolekcje lub inne pole jak liczby czy tekst. Dzięki temu będziemy mogli posegregować nasze wiadomości. Ale to wszystko będzie widoczne jak zaczniemy implementację. 

Stwórzmy teraz kolejną kolekcję, w dokumencie z kolekcji “czaty” :D.</div>



![](https://lh4.googleusercontent.com/TWf62OzkdzRmJVIZqXfUcNYbPHMkJ0g0afGHEm6W9MuIpiS0MNg1zBdtdO_b0537G96UxehtZ0EssgldP-rMXoiez4ggjkc0TgkndYUg7umhHIgyiuDn8EidWp4M-CFN4DDti0zd)



Nazwijmy ją oczywiście patriotycznie po polsku bo czemu nie (dobra obiecuję, że w kodzie nie będę pisał po polsku ;/ )



![](https://lh3.googleusercontent.com/WLZ1hDuD60G3XE4QN0oVJ48rlHDVIlgh47mUM4HePZlIR7T6xoBKfsNDRmaWsuqHBzY9ZntQMHtgRSzBE4ONmLgAZdlWNPKmmXohGoLA4qiVNGwsRJwTX1A7t0eoWjsy5qThY2z-)



<div style="text-align: justify;">Klikamy dalej i pojawi się już znane nam okno. Znowu wybieramy automatyczne id i dodajemy nowe pole typu string oczywiście o nazwie dowolnej i dowolnej treści, to będzie jedna z naszych wiadomości. Ja swoje pole nazwałem “tekst” i napisałem w nim coś mądrego.</div>



![](https://lh5.googleusercontent.com/j9x4u8hKRDadQh44yKAFJ_IUC4VZgKm_L2kJnl4xRsEOEWtZBLN1-Kf_JIGXGm1C3RbLrqoZ8AiH5G8IJ57pfIYrl99cOpXTgwmK4myNWzU8K6Gu8Yr5ygOlu4eBRvcKDPtuM9aS)

<div style="text-align: justify;">Zapisujemy i mamy stworzoną kolekcję z przykładowymi danymi.</div>

<div style="text-align: justify;">Dobrze czas przejść do kodu bo od tego klikania tylko palec boli. Wchodzimy do naszego ulubionego IDE (ja nie mam ulubionego ale przejdę do VSC bo się najszybciej urochomi :D)

W głównym katalogu stworzymy sobie folder “screens” dla plików dart które będą odpowiadały pojedynczym ekranom w aplikacji. Pierwszym będzie oczywiście chat_screen.dart, a w drugim zrobimy sobie logowanie czyli auth_screen.dart tworzymy te dwa pliki i uzupełniamy podstawowy kod. W VSC wystarczy użyć skrótu wpisując “st” pojawi nam się podpowiedź stateful czy stateless widget. Oczywiście na początku możemy wybrać stateless widget w razie czego zawsze można go przekonwertować na stateful. Wpisujemy nazwę klasy, którą będziemy się posługiwać, aby odnosić się do tego ekranu w naszej aplikacji. Plik uzupełniamy o import material.dart z package:flutter czyli podstawową paczkę fluttera zawierającą większość klas używanych do budowania Material UI.

W return zwracamy Scaffold czyli klasę zawierającą podstawowe komponenty potrzebne dla każdego nowego ekranu. Ustawmy sobie “appBar” wraz z tytułem oraz “body” czyli jak sama nazwa wskazuje treść, którą chcemy wyświetlić na ekranie. Ustawimy tu sobie widok listy za pomocą ListView.builder używamy go do budowania list, w których nie wiemy jaka jest liczba elementów, ale z dużym prawdopodobieństwem podejrzewamy, że może być długa. Jeśli wiemy, że na naszej liście zawsze będzie tylko kilka elementów możemy użyć “zwykłego” ListView. ListView.builder wymaga dwóch argumentów: itemCount(określa ile elementów będzie w liście) oraz itemBuilder( przypisujemy do niego funkcję anonimową przyjmującą kontekst naszej aplikacji oraz indeks, którym posługujemy się aby odnosić do poszczególnych elementów z listy ). Na razie w ciele funkcji wypiszmy tylko, że jest pojedynczym elementem. Dobra to napisz to teraz wiem, że opisałem to w tak wspaniały sposób, malując wizję w Twojej głowie niczym Picasso malował kiedyś swoje obrazy albo nawet niczym student przemalowywujący wykład z tablicy na przedmiocie, którego nie kuma.</div>

A wszystko wygląda mniej więcej tak:



```dart
import 'package:flutter/material.dart';
 
class ChatScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Czatujemy")),
      body: ListView.builder(
        itemCount: 5,
          itemBuilder: (ctx, index) => Container(
                child: Text('pojedynczy element'),
              )),
    );
  }
}
```



<div style="text-align: justify;">I co masz podobnie? Nie? Hmm.. być może nie wytłumaczyłem tego tak pięknie jak mi się zdawało :(, ale nic straconego najważniejsze to się nie poddawać dlatego kod w pliku auth_screen.dart spróbuj napisać samodzielnie :D. Dobra, ale dalej nie mamy naszego połączenia z bazą trzeba je stworzyć.<div>

Importujemy nasz plugin:



```dart
import 'package:cloud_firestore/cloud_firestore.dart';
```



<div style="text-align: justify;">Teraz zaimplementujemy połączenie za pomocą klasy StreamBuilder. Klasę dostarcza Flutter nie jest to klasa z pakietu firestore. Innym sposobem jest wykorzystanie klasy FutureBuilder czym one się różnią? W skrócie StreamBuilder pozwala na aktualizację danych w czasie rzeczywistym (czyli właśnie tzw. streamowanie) Natomiast FutureBuilder aktualizuje dane na żądanie. Innymi słowy zmieniając dane w naszej bazie danych w konsoli firebase aktualizacja automatycznie pojawia się w naszej aplikacji (używając StreamBuilder). Kiedy używamy FutureBuilder musimy wykonać jakieś działanie, aby ponowić żądanie danych ( np. odświeżenie za pomocą pociągnięcia w dół na ekranie). Oczywiście możecie zajrzeć w dokumentację żeby poznać więcej szczegółów.

Dobrze owiniemy teraz nasz ListView.builder w klasę StreamBuilder. Na szczęście możemy to zrobić bardzo szybko klikając na nią prawym klawiszem myszy -> refactor -> wrap with StreamBuilder. Od razu pojawią się dwie własności klasy stream(ustawione na null) oraz builder(w nim anonimowa funkcja przyjmująca context i snapshot czyli kontekst aplikacji oraz ostatnią instancję danych, zawierająca kod, który wcześniej poddaliśmy refaktoryzacji do StreamBuilder). W “stream” musimy umieścić instancje naszej bazy wraz ze ścieżką kolekcji, do której chcemy się dobrać. Dzięki pluginowi robimy to bardzo prosto:</div>



```dart
stream: Firestore.instance
        .collection('/czaty/tu-wygenerowany-id/wiadomosci')
        .snapshots(),
```

<!--StartFragment-->

Zadeklarujmy zmienną i przypiszmy do niej dokument z bazy danych podany w ścieżce wyżej:

<!--EndFragment-->

```dart
final doc = snapshot.data.documents;
```



teraz w builder zamieniamy “itemCount” z 5 na:



```dart
itemCount: doc.length,
```



zwraca nam to długość naszej kolekcji elementów do wyświetlenia. I oczywiście podmieńmy statyczny tekst na pojedynczy element z bazy danych:



```dart
child: Text(doc[index]['tekst']),
```



Oczywiście w pliku main.dart w domyślnej klasie MyApp usuńmy wygenerowaną automatycznie klasę i za nią wstawmy w “home” naszą ChatScreen().

Teraz odpalmy aplikację na emulatorze lub naszym smartfonie.



![](https://lh4.googleusercontent.com/79Ll82mA0K8UxuRvrgyoQFquWGOBeViIwhuIm-eXeJrmHA6j-gyRYpeSf6q5_3XQnTIJtg4QjqZ2FNyGyNpaxpwSscWPiz55Tu5uGQz3mKtriTs7UDgXsPxLmG-1gmyh5Xy1Kacu)



No i działa może nie wygląda najlepiej, ale działa :D.

Koniec części 1.