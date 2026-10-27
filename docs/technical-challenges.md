# Wyzwania techniczne

Ten dokument opisuje problemy inżynierskie widoczne w projekcie systemu oraz zastosowane podejście. Nie ujawnia danych klienta i nie przypisuje projektowi niezmierzonych wyników.

## 1. Jeden dzień operacyjny, wiele przepływów pracy

**Problem.** Organizatorzy, recepcja, kierownicy zmiany, kuchnia, animatorzy, HR i serwis współdzielą informacje, ale podczas zmiany potrzebują różnych działań. Widok działu nie jest tym samym co tożsamość ani uprawnienia przypisane do urządzenia.

**Podejście.** System rozdziela rolę personelu od widoku działu. Uprawnienia wynikające z roli chronią poszczególne przepływy, a widoki działów pokazują odpowiednie zadania na wspólnych rekordach operacyjnych. Zmiany rezerwacji trafiają do historii wraz z rolą wykonującą zapis; usunięte rezerwacje mają osobną historię.

**Rezultat.** Personel może przechodzić między widokami zadań bez niejawnej zmiany roli urządzenia, a ważne zmiany rezerwacji pozostają możliwe do prześledzenia. Historia nie identyfikuje konkretnej osoby — zapisuje rolę, a nie imię i nazwisko.

## 2. Zewnętrzne systemy mają różne formaty danych i dostępność

**Problem.** Dane o rezerwacjach i menu pochodzą z integracji POS, grafiki personelu z arkusza, a raportowanie wymaga stabilnych migawek miesięcznych. Używanie każdego zewnętrznego odczytu jako bieżącego źródła prawdy uzależniałoby codzienną pracę od dostępności tych usług.

**Podejście.** Kod integracyjny jest rozdzielony według usług. Wybrane dane katalogowe POS są przechowywane w lokalnym odwzorowaniu, a lokalne pojęcia operacyjne są mapowane na zewnętrzne identyfikatory produktów. Dane raportowe zapisuje się w migawkach dla widoków miesięcznych. Grafiki są odświeżane ze źródła zamiast traktować je jako dane personelu należące do aplikacji.

**Rezultat.** Granice zewnętrznych integracji są jawne, a przepływy pracy nie muszą bezpośrednio zawierać identyfikatorów konkretnych dostawców. Case study nie deklaruje określonej dostępności usługi ani opóźnień synchronizacji.

## 3. Rezerwacje rozwijają się wraz z potrzebami operacyjnymi

**Problem.** Rezerwacja może obejmować wiele dodatków, tortów, pozycji menu, przypisań personelu, zmian statusu i późniejszych korekt. Pojedynczy płaski rekord staje się trudny do rozwijania i audytowania, gdy dochodzą nowe przepływy pracy.

**Podejście.** Model relacyjny traktuje rezerwację jako rekord centralny, a powtarzalne lub historyczne dane przechowuje w powiązanych zbiorach, takich jak historia rezerwacji, pozycje menu, zapotrzebowanie magazynowe i migawki sprzedaży. Zmiany schematu są wersjonowane jako migracje.

**Rezultat.** Model obsługuje bogatsze szczegóły operacyjne, zachowując rezerwację jako wspólny punkt odniesienia dla kolejnych zadań. Dokładne definicje tabel i pola specyficzne dla klienta nie są publikowane.

## 4. Zmiany schematu produkcyjnego wymagają jawnych zabezpieczeń

**Problem.** Migracja uruchamiana automatycznie przy starcie aplikacji może zamienić wdrożenie w nieprzejrzystą zmianę danych produkcyjnych.

**Podejście.** Start aplikacji w produkcji pomija inicjalizację schematu. Migracje uruchamia się osobnym poleceniem, które obsługuje tryb próbny; zastosowanie zmian produkcyjnych wymaga dodatkowego jawnego potwierdzenia. Środowisko testowe korzysta z odizolowanej bazy.

**Rezultat.** Wdrożenie aplikacji i zmiana schematu produkcyjnego są oddzielnymi, możliwymi do przejrzenia operacjami. Ogranicza to ryzyko przypadkowych zmian podczas restartu, ale nie zastępuje kopii zapasowych ani sprawdzonego planu odtwarzania.

## Dalsze potwierdzenie

Oryginalna implementacja zawiera testy dotyczące rezerwacji, dostępu, integracji, raportów i migracji. To publiczne case study nie zawiera plików źródłowych ani testów; nie podaje liczby testów ani procentowego pokrycia.
