---
template: post
title: "Prosty komunikator z użyciem Flutter i Firebase #3"
slug: komunikator-flutter-firebase-3
draft: false
date: "2020-05-10T17:10:35.367Z"
description: "Prosty komunikator z użyciem Flutter i Firebase #3"
category: development
tags:
  - "#flutter"
  - "#dart"
  - "#firebase"
---
W tej części zmodyfikujemy nasz formularz dodając walidację i przejście z logowania do rejestracji nowego konta. Mamy nasz poprzedni kod. Teraz do każdego z naszych TextFormField dodamy własność “validator” z odpowiednią logiką.



W pierwszym polu tekstowym dodajemy warunek, że nazwa użytkownika nie może być pusta i musi zawierać co najmniej 3 litery. Oczywiście możesz wymyślić swoją walidację nic nie stoi na przeszkodzie. Funkcja przyjmuje wartość “value” czyli to co użytkownik wpisuje w dane pole tekstowe.

```dart
validator: (value) {
    if (value.isEmpty || value.length < 3) {
         return 'Twoja nazwa musi zawierać co najmniej 3 litery';
    }
    return null;
},

```

<!--StartFragment-->



Drugi warunek dla adresu email jest podobny, zmieniłem tylko długość tekstu na konieczność występowania znaku “@” w adresie.

<!--EndFragment-->

```dart
if (value.isEmpty || !value.contains('@')) {
return 'Proszę wpisać prawidłowy adres email';
}
return null;
```

<!--StartFragment-->

Natomiast w haśle poza sprawdzeniem czy nie jest puste sprawdzimy czy ma minimum 7 znaków czyli minimalną ilość znaków w haśle akceptowaną przez firebase.

<!--EndFragment-->

```dart
if (value.isEmpty || value.length < 7) {
return 'Twoje hasło musi mieć co najmniej 7 znaków';
}
return null;
```

<!--StartFragment-->

Oczywiście w każdym przypadków zwracamy null jeśli podana przez użytkownika wartość jest prawidłowa.



Mamy nasze walidację jednak jak na razie nie są one w żaden sposób wyzwalane. Musimy je wszystkie uruchomić w momencie kiedy użytkownik naciśnie przycisk logowania. Zrobimy to za pomocą globalnego klucza funkcjonalności dostarczanej przez Flutter. Stwórzmy sobie taki klucz:

<!--EndFragment-->

```dart
class _AuthFormState extends State<AuthForm> {
  final _formKey = GlobalKey<FormState>();
```

<!--StartFragment-->

Klucz jak widać tworzymy w klasie _AuthFormState. GlobalKey zawiera FormState i dzięki temu typowi generycznemu możemy poinformować Flutter na czym będziemy operować, w tym przypadku na formularzu. I mała dygresja, podłoga czy inaczej podkreślenie w Dart tak jak w wielu innych językach programowania oznacza, że zmienna bądź klasa są prywatne czyli dostępne tylko lokalnie. Teraz bezpośrednio w Form dodajemy ten klucz dla własności “key”:

<!--EndFragment-->

```dart
//...
child: Form(
key: _formKey,
//...

```

<!--StartFragment-->

Teraz stworzymy funkcję, która pomoże nam sprawdzić czy wszystkie wprowadzone pola są prawidłowe.

<!--EndFragment-->

```dart
  void _checkSubmit() {
    final isValidate = _formKey.currentState.validate();
 
    if (isValidate) {
      _formKey.currentState.save();
    }
  }

```

<!--StartFragment-->

Posługując się naszym globalnym kluczem możemy uzyskać dostęp do formularza poprzez metodę currentState, a następnie validate(). Wywoła to wszystkie walidatory we wszystkich polach tekstowych w klasie Form, w której umieściliśmy nasz klucz oraz zwróci rezultat czy wszystkie są poprawne czy też nie. Zapisujemy to w zmiennej isValidate i jeśli jest prawdziwa chcemy przechwycić i zatwierdzić wartości z formularzy. Używamy do tego naszego klucza i na jego obecnym stanie wywołujemy metodę save(), która z kolei uruchamia wszystkie funkcje znajdujące się w naszych TextFormField przypisane do własności “onSaved”. Teraz stwórzmy trzy zmienne, do których zapiszemy już sprawdzone poprawne wartości.

<!--EndFragment-->

```dart
  String _userName = '';
  String _userEmail = '';
  String _userPassword = '';
```

<!--StartFragment-->

i analogicznie do poniższego przykładu w każdym z TextFormField we własności “onSaved” umieśćmy kod zapisujący dane do tych zmiennych:

<!--EndFragment-->

```dart
onSaved: (newValue) {
_userEmail = newValue;
},

```

<!--StartFragment-->

Dodajmy jeszcze linijkę kodu, który schowa klawiaturę po kliknięciu naszego przycisku logowania. Umieszczamy ją zaraz po sprawdzeniu poprawności danych w _checkSubmit():

<!--EndFragment-->

```dart
  void _checkSubmit() {
    final isValidate = _formKey.currentState.validate();
    FocusScope.of(context).unfocus();
//...

```

<!--StartFragment-->



W skrócie powoduje ona usunięcie wszystkich kursorów z pól tekstowych przez co nie ma gdzie czego wpisać co z kolei powoduje zamknięcie klawiatury (dość koślawe tłumaczenie, ale mam nadzieję, że w miarę zrozumiałe ;P). Oczywiście musimy jeszcze przypisać naszą funkcję do przycisku logowania:

<!--EndFragment-->

```dart
  RaisedButton(
child: Text('Logowanie'),
onPressed: _checkSubmit,
  ),

```

<!--StartFragment-->

Przypisujemy tylko adres funkcji czyli bez nawiasów ją wywołujących, ponieważ chcemy, aby jej wywołanie nastąpiło po naciśnięciu przycisku logowania. Dobrze teraz zajmijmy się przypadkiem kiedy użytkownik nie posiada konta i chce stworzyć nowe. Stwórzmy pomocną zmienną:

<!--EndFragment-->

```dart
  bool _isLogin = true;
```

<!--StartFragment-->

Pomoże nam ona w określeniu, który formularz wyświetlić w zależności od zachowania użytkownika. W języku dart możemy używać typów bezpośrednio, ale w naszych przypadkach nie są one konieczne, ponieważ nasze zmienne od razu zostały zainicjalizowane _isLogin na true, zmienne zapisujące dane formularzy na pusty String przez co możemy napisać słowo kluczowe “var” i Dart już będzie wiedział co za typ ma się tam pojawić. Chcemy, aby po tapnięciu użytkownika na “stwórz nowe konto” pojawił się odpowiedni formularz.

<!--EndFragment-->

```dart
  FlatButton(
  child:
    Text(_isLogin ? 'Stwórz nowe konto' : 'Mam już konto'),
    onPressed: () {
      setState(() {
                _isLogin = !_isLogin;
            });
         },
   ),

```

<!--StartFragment-->



Tak dokładnie po prostu przypisujemy przeciwną wartość po kliknięciu czyli jeśli będzie “true” to zmieni się na “false”. Kod ten umieszczamy w setState(), które ma za zadanie przerysować interfejs przy tej zmianie dzięki czemu wyświetli się pożądany formularz. A co się dzieje w Text? Używamy skróconego warunku if żeby zwrócić odpowiedni tekst dla przycisku po zmianie formularza:

<!--EndFragment-->

```dart
_isLogin ? 'Stwórz nowe konto' : 'Mam już konto'
  /\          /\              /\      /\
  ||          ||              ||      ||
warunek     zwróć to         else   zwróć to

```

<!--StartFragment-->

Analogicznie zmieniamy tekst w przycisku logowania (“Zaloguj” albo “Zarejestruj”). A teraz trochę magii Dart. Przy rejestracji nie chcemy wyświetlać pola z nazwą użytkownika. Chcemy tylko email i hasło. Z pomocą przychodzi lukier składniowy (polska nazwa wygrywa :D) Jeśli mamy np Column, a w niej tablice dzieci, to możemy w łatwy sposób dynamicznie pokazywać lub usuwać elementy umieszczając warunek przed którymś z nich. Jeśli zostanie spełniony element ten się pokaże, a jeśli nie no to go po prostu nie będzie. Więc przed polem tekstowym z nazwą użytkownika piszemy:



Jednak możliwość ta została dodana dopiero w Dart 2.3 więc najlepiej po prostu zmienić minimalną wersję dart w pubspec.yaml na którąś od 2.3 w górę:

<!--EndFragment-->

```yaml
environment:
  sdk: ">=2.7.0 <3.0.0"
```

<!--StartFragment-->

Nastepnie w terminalu:

`flutter pub get`

`flutter clean`



Czyli nasz kod teraz wygląda tak:

<!--EndFragment-->

```dart
 children: <Widget>[
       if (!_isLogin)
        TextFormField(
          validator: (value) {
            if (value.isEmpty || value.length < 3) {
              return 'Twoja nazwa musi zawierać conajmniej 3   
                     litery';
}

```

<!--StartFragment-->

Ok wygląda na to, że wszystko działa tylko mamy mały problem. Wpisałem jakieś przykładowe dane, w email jakiś niepoprawny email (co od razu pokazuje komunikat) i w polu hasło po prostu “haslo” i teraz co się dzieje. Po kliknięciu stwórz nowe konto wyświetla się prawidłowo drugi formularz jednak dane z pierwszego przechodzą do drugiego i oto ups.. moje hasło stało się widoczne i na dodatek znajduje się w polu adresu email.

<!--EndFragment-->

<!--StartFragment-->

![](https://lh6.googleusercontent.com/V300rzeurN_XnpG98rB_1YW6xTvjDmb9ehjrc-VV9GrKMvBeqXs_-gIQo9s4BWgd79NyRgq4kol_xRJYbCHRzU3ecX8BCpELGcO02Bu6snCBhElmqkh-UytRbdRJzEwzcUnwMpyk)

![](https://lh3.googleusercontent.com/M3yuY9vGmlt3PxmPjD1nVGgsuXagXafm4RpEAIUPll5kfjmtUfL8FIhefUdm4li8uMG-dnDpFE8Wq-kesoci_Fo_Xm7HHNliFQsJKDmAzS04WMKoYc66o5D0g6roRNuaAQgxu7w8)

<!--EndFragment-->

<!--StartFragment-->

Dlaczego tak się dzieje? Podczas dynamicznej zmiany wartości Flutter nie potrafi rozpoznać danych elementów poprawnie w tym samym widgecie dlatego użyjemy kluczy, aby te elementy zidentyfikować. Więcej tutaj:

<https://medium.com/flutter/keys-what-are-they-good-for-13cb51742e7d>

Stworzymy więc klucze ręcznie do każdego z TextFormField, dodając własność “key” wraz z klasą ValueKey, która będzie reprezentowała owy klucz.

<!--EndFragment-->

```dart
key: ValueKey('password'),
```

<!--StartFragment-->

Analogicznie do każdej zmieniamy tylko “password” na dowolny inny string identyfikujący poszczególne pole tekstowe. Teraz wystarczy “ctrl + s”. Nasz plik powinien się zapisać i automatycznie uruchomiony zostanie hot reload. Lub jeśli nie to używamy skrótu klawiszowego dla danego IDE. W VSC jest to “ctrl + f5” Wprowadzamy dane ponownie i viola po problemie ;)

<!--EndFragment-->