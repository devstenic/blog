---
template: post
title: "Czym jest Dart Null Safety?"
slug: sound-null-safety
draft: false
date: 2021-07-13T13:41:51.672Z
description: Sound Null Safety jest jednym z największych i najnowszych dodatków do języka Dart. Czym jest i w czym Ci może pomóc?
category: development
tags:
  - "#dart"
---
Zacznijmy od początku. Co było takiego złego w języku Dart przed wprowadzeniem do niego Null Safety?

Wyobraź sobie, że rozwijasz aplikację do zamawiania jedzenia, w której każdy z użytkowników musi podać swoje ulubione danie. Jednak ja nie mam ulubionego dania i zjem wszystko, co zaproponuje mi aplikacja (tak lubię jeść 😋). Co się stanie w późniejszym etapie, kiedy podczas pracy aplikacja, chcąc dla każdego użytkownika zamówić jego ulubione danie, wywoła na nim odpowiednią metodę?

```dart
for (var user in users) {
    user.favoriteMeal.orderMeal();
}
```

Oczywiście dla tych użytkowników, dla których ulubione jedzenie istnieje, aplikacja zadziała poprawnie, zamawiając danie (dla uproszczenia przyjmijmy, że wszystkie dania są dostępne). Natomiast, ponieważ ja nie ustaliłem swojego ulubionego dania domyślnie Dart, przypisał do tego pola null. W następstwie tego program przestał działać. Dlaczego? `Null` reprezentuje... nic... więc nie możemy zamówić niczego 😀. Konsekwencją tego jest nieoczekiwany błąd powodujący "wysypanie się" programu.

Szukanie i naprawianie takiego błędu może zająć dużo czasu i zasobów, co może z  łatwością przenieść się na straty finansowe i wizerunkowe, czego oczywiście nikt z nas nie chce. I tutaj wkracza Sound Null Safety. Statyczny analizator kodu Dart w czasie rzeczywistym sprawdza, czy napisany przez nas kod może zawierać niebezpieczne wartości `null`, które mogą przerwać działanie programu, a następnie informuje nas o błędach. Pytanie, co znaczy "Sound"? Sound Null Safety określamy język, w którym podczas uruchamiania aplikacji mamy 100% pewności, że błąd odwołania się do wartości null nie wystapi nigdy. 

Czy to jest jedyny plus tego rozwiązania? Oczywiście, że nie! Dzięki Sound Null Safety mamy mniejszy rozmiar oraz szybsze wykonywanie się aplikacji, ponieważ kompilator nie musi za każdym razem sprawdzać, czy dana wartość jest wartością null. Kompilator po raporcie analizatora kodu jest pewien, że żadna wartość nie może być pusta, co daje większe możliwości optymalizacji.

## System Typowania

Po wprowadzeniu Null Safety wszystkie typy są domyślnie non nullable (po polsku... niepuste?...). Oznacza to, że dopóki sami nie określimy specjalnego typu nullable (poprzez dodanie znaku zapytania), zmienna nie może zawierać wartości `null`:

```dart
int anything = null;
//A value of type 'Null' can't be assigned to a variable of type 'int'.
//Try changing the type of the variable, or casting the right-hand type to 'int'.
int? nullableAnything = null;
// OK.

String anything = null;
//A value of type 'Null' can't be assigned to a variable of type 'String'.
//Try changing the type of the variable, or casting the right-hand type to 'String'.
String? nullableAnything = null;
// OK.
```

Jak widzimy na powyższym przykładzie, deklarując zmienne standardowo, czyli: `int`, `double`, `String `itd. nie jest możliwe przypisanie do nich wartości `null`. Dostajemy informację od analizatora kodu, że wartość "Null" nie może zostać przypisana do typu `int`, musimy zmienić typ zmiennej (na` int?`) lub dokonać rzutowania na typ `int `(co w przypadku `null `nie zadziała).

Wszystko fajnie, ale zapytasz zaraz: "Skoro `null `jest taki zły to, czemu po prostu się go nie pozbyć całkowicie?". To jest dobre pytanie, jednak bardzo łatwo na nie odpowiedzieć. Czasami wartość `null `jest potrzebna. Wróćmy do przykładu aplikacji serwującej jedzenie. Konkretne danie na przykład pizza składa się z ciasta i różnych dodatków. Powiedzmy teraz, że jestem uczulony na laktozę i z mojej pizzy chce się pozbyć sera. Odznaczam więc ser w aplikacji, przez co wartość tego pola, wynosi `null`, a ponieważ w aplikacji na serze nie wywołuje żadnego działania (ser może być na pizzy lub nie) to `null `daje mi możliwość łatwego zweryfikowania czy dany dodatek ma zostać podany klientowi.

#### Typ Never

Wraz z pojawieniem się Null Safety pojawił się również nowy typ `Never`. Typ ten oznacza, że dany kwałek kodu nigdy nie może wykonać się z sukcesem. Przez co przeznaczeniem tego typu jest wyrzucenie wyjątku lub upewnienie się, że dany kod nigdy się nie wykona. Typ ten jest na tyle rzadko używany, że jeśli nie wiesz, czy go użyć w swojej aplikacji to prawdopodobnie nie jest Ci on do niczego potrzebny.

#### Promocja typów

Dzięki ulepszonej analizie przepływu (flow analysis) analizator kodu jest w stanie sprytnie określać czy dana wartość może być wartością null. Przykładowo:

```dart
// Bez null safety
bool isEmptyList(Object object) {
  if (object is! List) return false;
  return object.isEmpty; // <-- Error!
}

//Z null safety
bool isEmptyList(Object object) {
  if (object is! List) return false;
  return object.isEmpty; // <- OK!
}
```

Jak widzimy na powyższym przykładzie bez null safety, Analizator kodu zwraca błąd i nie pozwala go wykonać. Wynika to z tego, że analiza przepływu w poprzednich wersjach nie dawała możlwiości promocji typów. Oznacza to, że w przypadku bez null safety analizator kodu "myśli", że chcemy wywołać metodę `isEmpty `na obiekcie typu Object, który takiej metody nie posiada. Natomiast nowsza wersja analizatora "wyczytuje" z kontekstu, że funkacja zwróci `false`, jeśli obiekt nie będzie listą, przez co bez problemu, uznaje dalszy kod za poprawny, a mówiąc ściślej dokonuje w locie rzutowania na typ `List`  

`(object as List).isEmpty`

#### Null Assertion Operator

Nowy analizator kodu został usprawniony, ale nie jest nieomylny. Czasami zdarza się sytuacja, że jesteśmy pewni wywołania danej funkcji i że nigdy tam nie wystąpi `null` z pomocą przychodzi wtedy operator ` ! `  Służy do wymuszenia wywołania funkcji, jeśli analizator kodu informuje nas o błędzie, ale my jesteśmy w 100% pewni, że żaden błąd po wywołaniu nie nastąpi. 

#### Słowo kluczowe "late"

Zdarzają się sytuację, kiedy wiemy, że zmienna na pewno będzie zainicjowana tylko w późniejszym etapie. Najczęściej ten przypadek występuje, kiedy chcemy zainicjować zmienną w funkcji `initState()` przed stworzeniem widgetu stanowego (`StatefulWidget)`. Na przykład podczas tworzenia widgetu z Mapami Google:

```dart
class _MapViewBackgroundState extends State<MapViewBackground> {
  //...
  late double _lat;
  late double _lng;
  late LatLng _center;
  late GoogleMapController mapController;

  @override
  void initState() {
    _lat = widget.latitude;
    _lng = widget.longtitude;
    _center = LatLng(_lat, _lng);

    super.initState();
  }
  //...
```

Tworząc Mapę, wiemy, że zmienne `_lat `oraz `_lng `zostaną zainicjowane przed utworzeniem widgetu w funkcji `initState()`. Żeby poinformować o tym kompilator, oznaczamy te zmienne słowem kluczowym `late`.

Jednak to nie jedyna funkcja słowa kluczowego `late`.

```dart
class Count {
  late double heavyCounting = heavyCounting();
}

void main(List<String> arguments) {
  // zmienna heavyCounting nie jest tutaj inicjalizowana
  var counter = Count();
  // zmienna jest inicjowana dopiero tutaj
  counter.heavyCounting
}
```

Słowo kluczowe `late` pozwala nam na opóźnienie inicjalizacji zmiennych w obiekcie. Oznacza to, że zmienne zaznaczone jako `late `nie są inicjalizowane od razu przy konstrukcji obiektu, ale w momencie, kiedy dane pole klasy jest potrzebne. Jest to bardzo pomocne, kiedy inicjalizacjia obiektu jest kosztowna.

#### Podsumowanie

Czy warto korzystać z null safety? Moim zdaniem jak najbardziej. Jeśli jesteś na początku swojej drogi z programowaniem możliwe, że null safety może sprawić Ci trochę problemów, ale nie martw się, możesz bez przeszkód pominąć ten aspekt i pisać kod mniej bezpieczny, a wraz z upływem czasu dodawać null safety do swojej listy "do nauczenia". Jeśli programowałeś(-aś) w innych językach i przechodzisz na Dart zdecydowanie, polecam nauczyć się null safety od razu, będzie to procentowało w przyszłości, a Twój kod będzie bezpieczniejszy i szybszy. Na dzisiaj to tyle w kolejnych artykułach poruszymy dalsze tematy dotyczące Darta i Fluttera. 

Miłego kodowania! 😁