---
template: post
title: "Prosty komunikator z użyciem Flutter i Firebase #2"
slug: '"komunikator-flutter-firebase"'
draft: false
date: 2020-04-30T15:44:32.137Z
description: Komunikator z użyciem Flutter i Firebase
category: development
tags:
  - "#flutter"
  - "#firebase"
  - "#dart"
---
Dziś zajmiemy się naszym ekranem logowania. W poprzedniej części stworzyliśmy już plik auth_screen.dart prosiłem nawet o jego uzupełnienie :D oczywiście prośba ta była dla treningu, ale może już udało Ci się zrobić logowanie może już nawet napisać całą aplikację. Jeśli tak to nie pisz mi o tym bo ja jeszcze nawet dobrze nie zacząłem :(. W każdym razie już nadrabiam zaległości na początku stwórzmy sobie tym razem stateful widget. Oczywiście tak jak w poprzednim przypadku można to zrobić wpisując skrót “st” i wybierając stateful widget. Oczywiście po wygenerowaniu template-u importujemy paczkę material.dart.



Teraz zrobimy małą refaktoryzację. Ponieważ kod naszego formularza może być dość długi umieścimy go w osobnym widgecie. W tym celu w katalogu “lib” naszej aplikacji utwórzmy folder widgets w nim z kolei folder auth. Teraz nowy plik auth_form.dart. Formularz będzie oczywiście interaktywny, a więc tak samo tworzymy stateful widget. Teraz zaczyna się kaskadowe zagnieżdżanie :D (cokolwiek to znaczy..). Także po kolei. Na początek użyjemy widgetu Center, jak sama nazwa wskazuje służy on to wyśrodkowania treści w nim znajdującej się względem ekranu urządzenia. Dzieckiem Center będzie Card ten widget daje wygląd karty (jak widać większość widgetów nazywa się tak jaką mają rolę, zaskakujące :D ). Teraz chcemy żeby w tym polu co zajmuje Card był formularz, który w razie gdyby był za długi, można go przescrollować. Pomocnym widgetem będzie zatem SingleChildScrollView dostarczający taką funkcjonalność. Teraz możemy umieścić w jego wnętrzu Form zawierający Column. Nadążasz? Bo ja się zgubiłem. Trzeba to zapisać... ok już wiem co dalej. Teraz chcemy żeby nasza kolumna nie była wyższa niż to jej potrzebne dlatego jej własność “mainAxisSize” ustawimy na MainAxisSize.min. Column zachowuje się trochę jak beneficjent socjalistycznych programów rządowych więc zamiast własności “child” ma własność “children” dzięki czemu możemy w nim umieścić dowolną ilość widgetów jeden pod drugim. W naszym przypadku będzie to nazwa użytkownika, email i hasło. Tworzymy więc trzy klasy TextFormField. Z odpowiednimi polami oraz dwa przyciski służące do logowania i tworzenia nowego konta na razie bez żadnej funkcjonalności.

A cały zwracany widget wygląda tak:

```dart
   return Center(
      child: Card(
        child: SingleChildScrollView(
          child: Form(
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: <Widget>[
                TextFormField(
                  decoration: InputDecoration(labelText: 
                'Nazwa użytkownika'),
                ),
                TextFormField(
                  decoration: InputDecoration(labelText: 
            'Adres email'),
                  keyboardType: TextInputType.emailAddress,
                ),
                TextFormField(
                  decoration: InputDecoration(labelText: 
                'Hasło'),
                  obscureText: true,
                ),
                SizedBox(height: 10),
                RaisedButton(
                  child: Text('Logowanie'),
                  onPressed: () {},
                ),
                FlatButton(
                  child: Text('Stwórz nowe konto'),
                  onPressed: () {},
                ),
              ],
            ),
          ),
        ),
      ),
    );

```



Zmieńmy teraz ChatScreen na AuthScreen w main.dart, a także zwróćmy Scaffold wraz z AuthForm w własności “body” w auth_screen.dart, aby sprawdzić efekt naszej pracy.



<!--StartFragment-->

![](https://lh4.googleusercontent.com/M_fNf-oDzJ7lpPihWebeWQXnZLttVr9yoH-q5COB4hp68wLmjrebTraCtfYULuG83__JHBby9BVwofbTNVvOrKGeowYkpWV1ImZ7FAxqn6MRYxoDLp6tNTcrrsWMLOLoxflCwcbL)

<!--EndFragment-->

Małe poprawki. Dodamy margin do Card oraz sam formularz owiniemy w klasę Padding. W obu przypadkach umieściłem EdgeInsets.all(25) czyli margines 25 pikseli we wszystkie strony. I jeszcze raz:



![](https://lh5.googleusercontent.com/ICjHMTkJfFoJbUYVm2q8BawiUKfRJdw6pus9b_FlLYfDwvj0_g-uFvRuKz7KW3xSydl7CfVJASY6L4xEDNi3TH_JIOYlKIUE18P9ep1VuuSRkSf_f0_8oRI4cLfbYhQtZ7Fq9OPJ)



Już trochę lepiej. Dobrze w tej części to tyle w następnej zajmiemy się wyglądem i połączeniem formularza z ekranem logowania.