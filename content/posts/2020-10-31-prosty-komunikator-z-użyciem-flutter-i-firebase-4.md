---
template: post
title: "Prosty komunikator z użyciem Flutter i Firebase #4"
slug: prosty-komunikator-flutter-flirebase-4
draft: true
date: "2020-10-24T17:32:27.163Z"
description: '"Prosty komunikator z użyciem Flutter i Firebase #4"'
category: development
tags:
  - "#flutter"
  - "#dart"
  - "#firebase"
---
<!--StartFragment-->

W tej części zajmiemy się tworzeniem nowego użytkownika i implementacją uwierzytelniania. Stwórzmy sobie funkcję w _AuthScreenState, za pomocą której przekażemy wpisane w formularzu dane do naszego AuthForm.

<!--EndFragment-->

```dart

  void _authFormData(
      String userName, String email, String password, bool isLogin) {}
```

<!--StartFragment-->

Parametrami funkcji są dane naszego formularza oraz informacja czy chcemy się logować czy rejestrować. Następnie za chwilę w ciele tej funkcji użyjemy Firebase SDK do zalogowania użytkownika lub stworzenia dla niego nowego konta. Teraz połączymy nasze dane z ekranem autentykacji i klasą AuthForm, do której przekażemy funkcję. W tym samym pliku dodajemy _authFormData jako parametr dla konstruktora:

<!--EndFragment-->

```dart

      body: AuthForm(_authFormData),
```

<!--StartFragment-->



Teraz w klasie AuthForm trzeba takowy konstruktor stowrzyć, ponieważ na razie nie mamy żadnego. Przechodzimy więc do pliku auth_form.dart i dodajemy odpowiedni konstruktor oraz funkcję reprezentującą _authFormData.

<!--EndFragment-->

```dart
class AuthForm extends StatefulWidget {
  AuthForm(this.formData);
 
  final void Function(
      String userName, String email, String password, bool isLogin) formData;
//...

```

<!--StartFragment-->

Możemy teraz w klasie _AuthFormState w stworzonej przez nas wcześniej funkcji _checkSubmit odnieść się do funkcji przekazanej do naszego widgetu za pomocą “widget.formData”. Dodajmy od razu funkcję trim(), która pozbawi dane wszystkich pustych, niepotrzebnych znaków.

<!--EndFragment-->

```dart
    if (isValidate) {
      _formKey.currentState.save();
 widget.formData(_userName.trim(), _userEmail.trim(),_userPassword.trim(),
     _isLogin);
    }

```

<!--StartFragment-->

Już mamy wszystko przygotowane możemy teraz za pomocą Firebase SDK zrobić w pełni działające logowanie i rejestrację. Na początek pobierzmy i zainstalujmy plugin firebase_auth. Wchodzimy na stronę pub.dev, wpisujemy firebase auth

<!--EndFragment-->

<!--StartFragment-->

![](https://lh3.googleusercontent.com/9kZaLMhfRxBzMtjulPuG2w1IhTxnLlwbIpWrrNpW7qEzRiIONji7PTQibw_UrmIe5wRxqdbhBE3qYKM6qmX5bS4_2n2-_vS_8-ReYjRhqMR96Fw4VBcs5SxDIzayCLF9VryF8r4m)

<!--EndFragment-->

<!--StartFragment-->

Wybieramy pierwszą pozycję, następnie przechodzimy do zakładki “installing” i wykonujemy poniższe instrukcje

<!--EndFragment-->

<!--StartFragment-->

![](https://lh6.googleusercontent.com/8NjT1YbR5n_xDy0M__ivFuANwjAFK0I5fhcjPPRNvW7TgWK1EBcQ_F12MGu9cWICN6JPWoeak_T8bqpq8scW-rjkwz3tJ2Hn02zV0T13nKDhCKZ83Sppco6nXgHTWcm9wM8q7h80)

<!--EndFragment-->

<!--StartFragment-->

w pliku pubspec.yaml dodajemy plugin do “dependencies”

<!--EndFragment-->

```yaml
  firebase_auth: ^0.16.0
```

<!--StartFragment-->

i zapisujemy plik (co powinno wywołać automatycznie pobieranie pakietów) i ewentualnie w konsoli wpisujemy “flutter pub get”.



Teraz włączmy nasze uwierzytelnianie w konsoli firebase Na stronie głównej naszego projektu przechodzimy do “Authentication”.

<!--EndFragment-->

<!--StartFragment-->

![](https://lh3.googleusercontent.com/14cJzmI-8csKZvTudcno6iPIxysxjkjJw3y8Ux8s7wg8CPebrJ8QpLHx_bS8yyJaxrNSbDKyGVE6TuglbZktXLI77dNYnbR_AocwLcVHnSV_yOmwl2S3cu_EnH5659vLRl0J4jxQ)

<!--EndFragment-->

<!--StartFragment-->



Następnie “sign-in method” klikamy na “E-mail/hasło” i włączamy pierwszą opcję oraz zapisujemy:

<!--EndFragment-->

<!--StartFragment-->

![](https://lh6.googleusercontent.com/Z9_VgBbVPhkRKbmoPUbOTK3Jm1ZkCsVerpJEQJOmZ1qY7zeaJ7QFkVmKzad7W4nsPOPvw9V2UlDPJE3CHkpZ6xzwrzto45oh5UZwFV1c8hcIjlq_ioi_zNMYOPVb2XEdBeOFDULB)

<!--EndFragment-->

<!--StartFragment-->

Możemy oczywiście włączyć i skorzystać z innych metod logowania np. przez portale społecznościowe itd. Ograniczmy się jednak do tradycyjnego sposobu za pomocą email i hasła. Zaimportujmy nasz plugin:

<!--EndFragment-->

```dart
import 'package:firebase_auth/firebase_auth.dart';
```

<!--StartFragment-->

Utwórzmy teraz w klasie _AuthScreenState prywatną zmienną reprezentującą instancję naszej klasy pochodzącej z Firebase SDK używaną do autentykacji:

<!--EndFragment-->

```dart
  final _auth = FirebaseAuth.instance;
```

<!--StartFragment-->



Teraz pozostało już nam tylko zakodować funkcję _authFormData. Wszystkie operacje wymagające połączenia z firebase w celu autentykacji są asynchroniczne. Oznacza to, że wysyłamy jakieś żądanie i musimy poczekać na odpowiedź zanim coś z nią zrobimy. Dlatego też oznaczmy naszą funkcję słowem kluczowym “async” . Następnie podzielmy logicznie nasze operacje czyli użyjmy naszej flagi “isLogin” żeby w zależności od tego czy użytkownik próbuje się zalogować czy zarejestrować wysłać odpowiednie żądanie do Firebase. Stwórzmy teraz zmienną authResult o typie AuthResult dostarczanym przez Firebase. Do niej zapiszemy rezultat naszego żądania. Wykorzystując metody na naszej Firebase.instance “signInWithEmailAndPassword” w przypadku logowania oraz “createUserWithEmailAndPassword” w przypadku rejestracji. Obie operacje są asynchroniczne i zwracają typ “Future” więc “czekamy” na ich rezultat dodając słowo kluczowe “await”. Zawsze może coś pójść nie tak więc żeby uniknąć wywalenia się naszej aplikacji cały dotychczasowy blok kodu owijamy w try catch. Catch czyli łapać, a więc złapmy błędy. Pierwsze z serii “PlatformException” czyli błędy wyrzucane przez firebase gdy coś jest nie tak. A drugie to te wszystkie inne. Teraz niech ten błąd będzie widoczny dla użytkownika. Wyrzucimy ładny snackbar z komunikatem, ale żeby to zrobić potrzebujemy kontekstu z klasy AuthForm, ponieważ AuthScreen nie jest połączona z tym kontekstem i snackbar nie zadziała. W tym przypadku zwyczajnie przekażemy kontekst w konstruktorze klasy AuthForm. A kod funkcji wygląda tak:

<!--EndFragment-->

```dart
  void _authFormData(
      String userName, String email, String password, bool  
     isLogin, BuildContext ctx) async {
    AuthResult authResult;
    try {
    if (isLogin) {
      authResult = await _auth.signInWithEmailAndPassword(
          email: email, password: password);
    } else {
      authResult = await _auth.createUserWithEmailAndPassword(
          email: email, password: password);
    }
    } on PlatformException catch (error) {
      var errorMessage = 'Wystąpił błąd poczas logowania';
      if(error.message != null) {
        errorMessage = error.message;
      }
      Scaffold.of(ctx).showSnackBar(SnackBar(content:
     Text(errorMessage),));
    } catch (error) {
      print(error);
    }
  }

```

<!--StartFragment-->

przetestujmy działanie naszej funkcji:

<!--EndFragment-->

<!--StartFragment-->

![](https://lh3.googleusercontent.com/INN3kactz3W2NRhohB67PGvCZmRySkbg87wyv7t59c_ZYbeqoOIn_uqUqYjhhOOPVXkMN7WpzRRBW1lD2PZBqY8RTNHGppgznKbq0NCE_hvJgvIrZVRJcQQibF3p7LzfaRrcAohx)

<!--EndFragment-->

<!--StartFragment-->

![](https://lh5.googleusercontent.com/hLuFE0utWmocn3R3iqSzkGLcZXVYhWAQ2vF_YQ5qlbb4lCrsmop6eMQ8x1kUImWCq6eLyjsP1rkzSObfm0atfKIyXHQIGWxM42ValzAYAlB1ZZ6-uPYz41T7SCRMHpBPui0oMhCO)

<!--EndFragment-->

<!--StartFragment-->

I jak widać nowy użytkownik został dodany wszystko się udało. Oczywiście nasza weryfikacja nie jest jeszcze skończona. W następnej części dodamy kilka funkcji.

<!--EndFragment-->