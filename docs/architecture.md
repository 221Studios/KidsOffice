# Przegląd architektury

## Kształt systemu

Produkt jest niewielką aplikacją webową w Pythonie. Obsługuje przeglądarkowy interfejs mobilny i przepływy operacyjne. CSS jest budowany jako zasób przed wdrożeniem, a aplikację pakuje się do Dockera i uruchamia na hostowanej platformie.

```mermaid
flowchart TB
    Browser[Interfejs przeglądarkowy<br/>HTML, CSS, JavaScript] -->|żądania HTTPS| Web[Aplikacja HTTP w Pythonie]
    Web --> Domain[Przepływy rezerwacji i operacji]
    Domain --> DB[(Baza relacyjna)]
    Domain --> POSAdapter[Integracja POS]
    Domain --> ScheduleAdapter[Integracja grafików]
    Domain --> Notifications[Wysyłka powiadomień push]
    POSAdapter <--> POS[API POS]
    ScheduleAdapter --> Sheet[Arkusz grafików]
    Notifications --> PushSvc[Usługa Web Push]
    Container[Obraz Docker] -. działa na .-> Runtime[Hostowane środowisko]
    Runtime --> DB
```

## Trwałość danych i ich pochodzenie

- **Rekordy operacyjne:** rezerwacje, historia zmian, przypisania personelu, inwentura i powiązane zadania.
- **Zewnętrzne dane referencyjne:** wybrane dane menu i produktów POS, odświeżane i lokalnie odwzorowywane tam, gdzie są potrzebne.
- **Grafiki:** pobierane z arkusza i odświeżane w widokach aplikacji.
- **Migawki raportowe:** zapisane wartości używane w historycznych raportach, oddzielone od bieżącej edycji rezerwacji.
- **Lokalny rozwój:** SQLite upraszcza uruchomienie środowiska.
- **Wdrożenie hostowane:** PostgreSQL przechowuje dane relacyjne.

## Konceptualne relacje encji

```mermaid
erDiagram
    RESERVATION ||--o{ RESERVATION_HISTORY : ma
    RESERVATION ||--o{ MENU_LINE : zawiera
    RESERVATION ||--o{ INVENTORY_LINE : wymaga
    INVENTORY_ITEM ||--o{ INVENTORY_LINE : jest_powiazany_z
    INVENTORY_ITEM ||--o{ INVENTORY_MOVEMENT : rejestruje
    WORK_DATE ||--o{ ZONE_ASSIGNMENT : grupuje
    REPORTING_PERIOD ||--o{ BANQUET_SNAPSHOT : zapisuje
    REPORTING_PERIOD ||--o{ TABLE_SALES_SNAPSHOT : zapisuje
```

Nazwy encji są uogólnione na potrzeby portfolio. Diagram nie jest eksportem produkcyjnego schematu.

## Zasady integracji

1. Wywołania dostawców zewnętrznych pozostają za modułami integracyjnymi.
2. Dane operacyjne należące do aplikacji są odróżnione od danych odczytywanych ze źródeł zewnętrznych.
3. Wartości raportowe są zapisywane w migawkach, gdy potrzebny jest stabilny widok historyczny.
4. Baza środowiska testowego jest odizolowana od produkcyjnej.
5. Zmiany schematu produkcyjnego wykonuje się jawnym poleceniem migracji, a nie podczas startu aplikacji.

## Dobór technologii

| Obszar | Technologia | Zastosowanie |
|---|---|---|
| Aplikacja | Python 3.12+ | Obsługa HTTP i przepływy operacyjne |
| Interfejs | HTML, CSS, JavaScript | Mobilny interfejs w przeglądarce |
| Budowanie CSS | Vite i PostCSS | Budowanie i minifikacja zasobów CSS |
| Lokalna baza | SQLite | Samodzielne środowisko deweloperskie i lokalna demonstracja |
| Baza hostowana | PostgreSQL | Relacyjna baza danych dla wdrożenia hostowanego |
| Wdrożenie | Docker i Fly.io | Powtarzalny obraz aplikacji i hostowane środowisko |
| Integracja POS | API Syrve | Przepływy rezerwacji, menu i raportowania |
| Źródło grafik | Google Sheets API | Odczyt grafików personelu |
| Powiadomienia | Web Push | Powiadomienia przeglądarkowe |
