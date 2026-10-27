# Wyzwania techniczne

Ten dokument opisuje problemy inżynierskie widoczne w projekcie systemu oraz zastosowane podejście. Nie ujawnia danych klienta i nie przypisuje projektowi niezmierzonych wyników.

## 1. Jeden dzień operacyjny, wiele przepływów pracy

**Problem.** Organizatorzy, recepcja, kierownicy zmiany, kuchnia, animatorzy, HR i serwis współdzielą informacje, ale podczas zmiany potrzebują różnych działań. Widok działu nie jest tym samym co tożsamość ani uprawnienia przypisane do urządzenia.

**Podejście.** Rozdzieliliśmy rolę personelu od widoku działu. Uprawnienia wynikające z roli chronią poszczególne przepływy, a widoki działów pokazują odpowiednie zadania na wspólnych rekordach operacyjnych. Zmiany rezerwacji zapisujemy w historii wraz z rolą wykonującą zapis; usunięte rezerwacje mają osobną historię.

**Rezultat.** Dzięki temu personel może przechodzić między widokami zadań bez niejawnej zmiany roli urządzenia, a ważne zmiany rezerwacji pozostają możliwe do prześledzenia. Historia nie identyfikuje konkretnej osoby — zapisuje rolę, a nie imię i nazwisko.

## 2. Zewnętrzne systemy mają różne formaty danych i dostępność

**Problem.** Dane o rezerwacjach i menu pochodzą z integracji POS, grafiki personelu z arkusza, a raportowanie wymaga stabilnych migawek miesięcznych. Używanie każdego zewnętrznego odczytu jako bieżącego źródła prawdy uzależniałoby codzienną pracę od dostępności tych usług.

**Podejście.** Rozdzieliliśmy kod integracyjny według usług. Wybrane dane katalogowe POS przechowujemy w lokalnym odwzorowaniu, a lokalne pojęcia operacyjne mapujemy na zewnętrzne identyfikatory produktów. Dane raportowe zapisujemy w migawkach dla widoków miesięcznych. Grafiki odświeżamy ze źródła, zamiast traktować je jako dane personelu należące do aplikacji.

**Rezultat.** Dzięki temu granice zewnętrznych integracji są jawne, a przepływy pracy nie muszą bezpośrednio zawierać identyfikatorów konkretnych dostawców. Nie deklarujemy określonej dostępności usług ani opóźnień synchronizacji.

## 3. Rezerwacje rozwijają się wraz z potrzebami operacyjnymi

**Problem.** Rezerwacja może obejmować wiele dodatków, tortów, pozycji menu, przypisań personelu, zmian statusu i późniejszych korekt. Pojedynczy płaski rekord staje się trudny do rozwijania i audytowania, gdy dochodzą nowe przepływy pracy.

**Podejście.** Przyjęliśmy model relacyjny, w którym rezerwacja jest rekordem centralnym, a powtarzalne lub historyczne dane przechowujemy w powiązanych zbiorach, takich jak historia rezerwacji, pozycje menu, zapotrzebowanie magazynowe i migawki sprzedaży. Zmiany schematu wersjonujemy jako migracje.

**Rezultat.** Model obsługuje bogatsze szczegóły operacyjne, zachowując rezerwację jako wspólny punkt odniesienia dla kolejnych zadań. Nie publikujemy dokładnych definicji tabel ani pól specyficznych dla klienta.

## 4. Zmiany schematu produkcyjnego wymagają jawnych zabezpieczeń

**Problem.** Migracja uruchamiana automatycznie przy starcie aplikacji może zamienić wdrożenie w nieprzejrzystą zmianę danych produkcyjnych.

**Podejście.** Skonfigurowaliśmy aplikację tak, aby w produkcji pomijała inicjalizację schematu przy starcie. Migracje uruchamiamy osobnym poleceniem z trybem próbnym; zastosowanie zmian produkcyjnych wymaga dodatkowego jawnego potwierdzenia. Środowisko testowe korzysta z odizolowanej bazy.

**Rezultat.** Oddzieliliśmy wdrożenie aplikacji od zmiany schematu produkcyjnego, dzięki czemu są to dwie możliwe do przejrzenia operacje. Ogranicza to ryzyko przypadkowych zmian podczas restartu, ale nie zastępuje kopii zapasowych ani sprawdzonego planu odtwarzania.

## Dalsze potwierdzenie

Oryginalna implementacja zawiera testy dotyczące rezerwacji, dostępu, integracji, raportów i migracji. To publiczne case study nie zawiera plików źródłowych ani testów; nie podaje liczby testów ani procentowego pokrycia.
