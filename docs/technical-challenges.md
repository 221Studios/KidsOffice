# Decyzje techniczne

## Uprawnienia nie zależą od aktualnego ekranu

Telefon personelu ma przypisaną rolę, natomiast użytkownik może przełączać widoki działów. Zdecydowaliśmy się rozdzielić te dwa pojęcia: zmiana ekranu nie zmienia roli ani zakresu dostępu urządzenia. Dzięki temu organizator może przejść do widoku kuchni, nie zmieniając kontekstu uprawnień.

Zmiany rezerwacji zapisujemy w historii razem z rolą, która wykonała operację. Usunięte rezerwacje zachowują osobny wpis historii. Audyt wskazuje rolę urządzenia, a nie imienną tożsamość pracownika.

## Jeden model rezerwacji, dwa różne procesy

Bankiet urodzinowy i rezerwacja stolika nie są tym samym formularzem z opcjonalnymi polami. Bankiet obejmuje dziecko, dodatki, menu i przygotowania wielu działów; rezerwacja stolika ma inny zestaw danych. Rozdzieliliśmy te typy, a wspólne elementy — jak termin, goście i historia — pozostawiliśmy powiązane z rezerwacją.

Model obsługuje też wiele tortów na jednej rezerwacji, każdy z własnym zdjęciem, oraz oddzielne pozycje menu. Zadania kuchni i animatorów mogą dzięki temu korzystać z konkretnych informacji z karty zamiast z jednego zbiorczego opisu.

## Integracje bez uzależniania każdego widoku od dostawcy

Syrve dostarcza dane o rezerwacjach, menu, produktach i sprzedaży; grafiki pochodzą z Google Sheets. Wydzieliliśmy obsługę tych źródeł, a wybrane dane katalogowe POS odwzorowujemy lokalnie. Lokalny klucz produktu jest mapowany na identyfikator Syrve, dzięki czemu widoki operacyjne nie muszą opierać całego modelu na identyfikatorach dostawcy.

Statystyki zapisują migawki okresowe. Sprzedaż jedzenia z bankietów i sprzedaż codzienna ze stolików są raportowane osobno, a brak migawki nie jest prezentowany jako sprzedaż równa zero.

## Wdrożenie aplikacji i migracja bazy to osobne operacje

Nie uruchamiamy zmian schematu przy starcie serwisu produkcyjnego. Migrację można najpierw wykonać w trybie próbnym, a zastosowanie jej na produkcji wymaga jawnego potwierdzenia. Środowisko testowe korzysta z osobnej bazy.

To rozdzielenie zapobiega temu, by zwykły restart lub deploy aplikacji przypadkowo zmieniał strukturę produkcyjnych danych. Zmiana schematu pozostaje osobną operacją, którą można zaplanować i przejrzeć.
