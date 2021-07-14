---
template: post
title: Podłączenie Firebase do aplikacji Flutter
slug: firebase-flutter
draft: false
date: 2020-03-31T16:58:36.867Z
description: Konfiguracja Firebase z aplikacją Flutter
category: development
tags:
  - "#flutter"
  - "#firebase"
---
## Utworzenie projektu w firebase 

<div style="text-align: justify;">Wchodzimy na swoje konto firebase połączone z kontem google pod adresem: 

<https://console.firebase.google.com/>  

Następnie klikamy dodaj projekt.  

Wpisujemy nazwę projektu. Poprzez nazwę projektu firebase wygeneruje dla nas identyfikator, który jest unikalny globalnie i wykorzystywany w linku url dzięki czemu łatwo możemy łączyć się np. z bazą danych. Możemy pozostawić domyślny lub ustawić swój. 

Klikamy dalej i pojawia się nam okno z zapytaniem czy chcemy korzystać w naszej aplikacji z google analytics wraz z podstawowymi objaśnieniami do czego możemy je wykorzystać. Zaznaczamy lub odznaczamy wg uznania i klikamy dalej. 

Następnie decydujemy czy wybieramy już istniejące konto google analytics powiązane z naszym kontem firebase czy chcemy utworzyć nowe konto. Po dokonaniu wyboru nie pozaostało nam nic innego jak wybrać “utwórz projekt” i cierpliwie czekać jak spinner ładowania zakończy tworzenie naszego projektu 😊 </div>

## Dodanie naszej aplikacji do projektu Firebase 

<div style="text-align: justify;">Po utworzeniu projektu wyświetli nam się panel obsługi. Nadszedł teraz czas na zarejestrowanie naszej aplikacji w projekcie firebase. Jeśli chcemy zarejestrować naszą aplikację jednocześnie na kilka platform np. IOS i Android musimy to zrobić osobno dla każdej z nich. W panelu na stronie głównej (ikona domu z tekstem “opis projektu” klikając możemy przenieść się właśnie na tę stronę) dodajemy naszą aplikację: 

* Dla systemu android: 

1. Klikamy ikonę androida lub opcję “dodaj aplikację” (jeśli już wcześniej jakaś aplikacja została dodana do naszego proejktu) 
2. W następnym oknie wpisujemy nazwę pakietu aplikacji. Nazwa pakietu gdy nie martwisz się o nią podczas tworzenia nowego projektu we Fluterze lub Androidzie wygląda najcześciej tak: 

com.example.nazwaaplikacji 

Aby w naszej aplikacji sprawdzić jaką dokładnie nazwę nosi nasz pakiet należy wejsć do katalogu android/app i znaleźć plik build.gradle w nim </div>

```kotlin
android { 

 

    defaultConfig { 

        applicationId “tutaj znajdziesz nazwę pakietu” 

    } 

 
} 
```



<div style="text-align: justify;">

Jeśli Twoja aplikacja będzie przygotowywana pod Google Play warto zmienić nazwę pakietu na: 

com.nazwadomeny.nazwaplikacji 

Gdzie w polu “nazwadomeny” ustawiamy nazwę domeny naszej firmy lub domeny, na której mamy stronę promocyjną naszej aplikacji. 

Najłatwiejszym sposobem na zmianę nazwy pakietu jest ustalenie go na początku tworzenia projektu Flutter 😀 komendą: 

Flutter create   --org com.twojadomena nazwaaplikacji  

Lub 

Flutter create   --org com nazwaaplikacji 

Jeśli nie chcesz nazwy domeny w nazwie pakietu. 

Kiedy chcesz zmienić nazwę pakietu w już istniejącym projekcie możesz zrobić to na wiele sposobów jednak ponownie najszybszym będzie stworzeni nowego projektu na podstawie wyżej wymienionych komend oraz przekopiowanie do tego projektu katalogu “lib” oraz pliku “pubspec.yaml” z projektu źródłowego. 

Następnie możemy przejść do rzeczy opcjonalnych. 

* Pseudonim aplikacji - możemy ustawić własny pseudonim dla aplikacji, który jest tylko lokalny i ułatwia jej zarządzaniem z poziomu konsoli 
* Certyfikat SHA-1 służy do podpisywania aplikacji jest wymagany kiedy chcemy korzystać np. Z logowania poprzez konto Google do naszej aplikacji. Możemy kliknąć na informacje obok i dowiedzieć się jak go wygenerować lub uzupełnić go póżniej. 

Klikamy dalej i pobieramy plik konfiguracyjny w formacie “json” dla naszej aplikacji. 

Pobrany plik wklejamy do katalogu android/app w naszej aplikacji następnie w pliku build.gradle znajdującym się w katalogu głównym naszej aplikacji android/build.gradle dodajemy plugin Google Services: </div>



```kotlin
buildscript { 
 
    repositories { 
      // Sprawdź czy podana linia występuje jeśli nie dodaj ją 
      google()  // Google's Maven repository 
    } 
 
    // ... 
 
    dependencies { 
      // ... 
 
      // Dodaj tę linię 
      classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin 
    } 
} 
 
allprojects { 
    // ... 
 
    repositories { 
      // Sprawdź czy podana linia występuje jeśli nie dodaj ją 
      google()  // Google's Maven repository 
      // ... 
    } 
}
```



<div style="text-align: justify;">Następnie w pliku build.gradle na niższym poziomie czyli android/app/build.gradle dodaj następujący plugin: </div>



```kotlin
// dodaj tę linię 
apply plugin: 'com.google.gms.google-services'  // Google Services plugin 
 
android { 
  // ... 
} 
 
// … 
```



Po dodaniu w konsoli uruchom polecenie: 

`flutter packages get `

<div style="text-align: justify;">Po pobraniu zależności przechodzimy dalej w naszym projekcie w firebase możemy uruchomić naszą aplikacjie na emulatorze lub fizycznym urządzeniu i sprawdzić czy wszystko zostało zrobione poprawnie jeśli tak po uruchomieniu aplikacji w oknie firebase pokaże się komunikat potwierdzający zarejestrowanie aplikacji. Możemy także pominąć weryfikację poprawności i przejść dalej. <div>

## Dodawanie Pluginów FluterFire 

<div style="text-align: justify;">Pluginy FlutterFire zapewniają wygodną obsługe wszystkich funkcjonalności firebase wszystie pluginy możesz znaleźć na <https://firebaseopensource.com/projects/firebaseextended/flutterfire/> 

Zajmiemy się dodaniem tylko głównego pluginu resztę dodaje się analogicznie. 

1. Upewni się, że Twoja aplikacja jest wyłączona i nie działa na emulatorze ani fizycznym urządzeniu. 
2. Otwórz plik pubspec.yaml 
3. Dodaj poniższą linię: </div>

```yaml
dependencies: 
  flutter: 
    sdk: flutter 
  firebase_core: ^0.4.0+9
```

<div style="text-align: justify;">Pamiętaj, że plik yaml jest wrażliwy na “wcięcia” w tekście i definiują one strukturę pliku. 

Możesz dodać teraz wybrane pluginy np. : <div>

```yaml
dependencies: 
  flutter: 
    sdk: flutter 
  # Pamiętaj żeby dodać ten plugin najpierw 
  firebase_core: ^0.4.0+9 
 
  # Google Analytics 
  firebase_analytics: ^5.0.2 
 
 
  # Na przykład Firebase Authentication i Cloud Firestore 
  firebase_auth: ^0.14.0+5 
  cloud_firestore: ^0.12.9+5
```



Znów uruchom: 

`flutter packages get `

Gotowe 

Problemy: 

<div style="text-align: justify;">Po zainstalowaniu wszystkich zależności i probie uruchomienia aplikacji może pojawić się podobny problem : </div>



```textile
D8: Cannot fit requested classes in a single dex file (# methods: 83217 > 65536) com.android.builder.dexing.DexArchiveMergerException: Error while merging dex archives: 

… 
```



<div style="text-align: justify;">Występuję on wtedy jeśli w naszej aplikacji mamy ustawione wspieranie androida ponizej 5.0(API 21) Poprzednie wersje działały na maszynie wirtualnej Dalvik przez co trzeba dla nich włączyć obsługę multidex. Środowisko w nowszych wersjach zostało zamienione na ART. I problem ten nie występuje. Rozwiązanie: 

Dodaj poniższe biblioteki do pliku android/app/build.gradle: </div>



```kotlin
defaultConfig { 

        multiDexEnabled true 

} 
```



Następnie w konsoli wpisz: 

`flutter packages get `

<div style="text-align: justify;">I gotowe Twoja aplikacja jest połączona z Firebase! 😀 </div>