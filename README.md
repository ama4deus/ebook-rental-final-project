# Projekt Końcowy – Scenariusz i Testy API Systemu E-book Rental

---

## Wykaz Scenariuszy Testowych & Raport z Wykonania (Test Execution Report)

### MODUŁ 1: Zarządzanie Użytkownikami (`/users/`)
* **ST-01.01 (Pozytywny): Rejestracja nowego użytkownika (POST)** *(Postman request: `ST-01.01 Positive: Create user`)*
  * *Opis:* Dodanie nowego czytelnika do bazy danych.
  * *Oczekiwany rezultat:* Status 200 OK, serwer zwraca unikalny identyfikator użytkownika (`userId`).
  * *Status:* **PASSED** (Uzyskano ID: 6194)
* **ST-01.02 (Pozytywny): Pobranie listy użytkowników (GET)** *(Postman request: `ST-01.02 Positive: Get users list`)*
  * *Opis:* Wyświetlenie wszystkich zarejestrowanych osób.
  * *Oczekiwany rezultat:* Status 200 OK, lista obiektów użytkowników.
  * *Status:* **PASSED**

### MODUŁ 2: Katalog Książek (`/books/`)
* **ST-02.01 (Pozytywny): Dodanie nowej książki do katalogu (POST)** *(Postman request: `ST-02.01 Positive: Create book`)*
  * *Opis:* Utworzenie nowego tytułu (metadanych książki) w systemie.
  * *Oczekiwany rezultat:* Status 200 OK, serwer zwraca ID książki.
  * *Status:* **PASSED** (Uzyskano ID: 6198)
* **ST-02.02 (Pozytywny): Pobranie całego katalogu książek (GET)** *(Postman request: `ST-02.02 Positive: Get books list`)*
  * *Opis:* Wyświetlenie pełnej listy tytułów dostępnych w bibliotece.
  * *Oczekiwany rezultat:* Status 200 OK, lista obiektów JSON zawierająca dodane pozycje.
  * *Status:* **PASSED**

### MODUŁ 3: Zarządzanie Egzemplarzami (`/items/`)
* **ST-03.01 (Pozytywny): Pobranie listy egzemplarzy dla danej książki (GET)** *(Postman request: `ST-03.01 Positive: Get items list`)*
  * *Opis:* Sprawdzenie fizycznych kopii przypisanych do wybranego tytułu.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED**
* **ST-03.02 (Pozytywny): Dodanie nowego egzemplarza książki (POST)** *(Postman request: `ST-03.02 Positive: Create item`)*
  * *Opis:* Wprowadzenie do bazy nowej fizycznej sztuki książki o statusie "dostępna".
  * *Oczekiwany rezultat:* Status 201 Created, automatyczne nadanie daty zakupu.
  * *Status:* **FAILED (Wykryto Bug)** – System zwraca błąd 500, dopóki nie wymusi się ręcznego wpisania `purchaseDate` w ciele JSON. Po obejściu błędu uzyskano status 200 OK (ID egzemplarza: 6199).
* **ST-03.03 (Pozytywny): Usunięcie egzemplarza z systemu (DELETE)** *(Postman request: `ST-03.03 Positive: Delete item`)*
  * *Opis:* Skasowanie konkretnej kopii książki przy użyciu jej identyfikatora.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED**

### MODUŁ 4: Operacje Wypożyczeń (`/rents/`)
* **ST-04.01 (Pozytywny): Pobranie listy transakcji dla nowej kopii (GET)** *(Postman request: `ST-04.01 Positive: Get rents list`)*
  * *Opis:* Wyświetlenie historii wypożyczeń przypisanych do `itemId`.
  * *Oczekiwany rezultat:* Status 200 OK, pusta lista `[]` dla nowego egzemplarza.
  * *Status:* **PASSED**
* **ST-04.02 (Pozytywny): Nowe wypożyczenie – weryfikacja automatycznych dat (POST)** *(Postman request: `ST-04.02 Positive: Create new rental`)*
  * *Opis:* Tworzenie transakcji wypożyczenia dla klienta.
  * *Oczekiwany rezultat:* Status 201 Created, automatyczne wyliczenie daty zwrotu.
  * *Status:* **FAILED (Wykryto Bug)** – Backend nie generuje dat automatycznie (błąd 500). Dopiero po ręcznym dopisaniu pola `"rentDate": "2026-05-31"` system przepuścił żądanie (ID wypożyczenia: 6201).
* **ST-04.03 (Negatywny): Próba wypożyczenia niedostępnej książki (POST)** *(Postman request: `ST-04.03 Negative: Rent already rented copy`)*
  * *Opis:* Próba ponownego wypożyczenia egzemplarza, który jest już aktualnie zajęty przez innego klienta.
  * *Oczekiwany rezultat:* Status 400 Bad Request lub 409 Conflict. Blokada operacji.
  * *Status:* **FAILED (Krytyczny błąd logiczny)** – System zwrócił status 200 OK (ID: 6202) i pozwolił dwóm różnym klientom wypożyczyć tę samą fizyczną książkę w tym samym czasie. Brak walidacji statusu dostępności.
* **ST-04.04 (Pozytywny): Edycja trвающей wypożyczenia (PUT)** *(Postman request: `ST-04.04 Positive: Update rental details`)*
  * *Opis:* Zmiana danych lub przedłużenie terminu zwrotu w aktywnej transakcji.
  * *Oczekiwany rezultat:* Status 200 OK i zaktualizowany obiekt.
  * *Status:* **FAILED (Krytyczny Bug)** – Endpoint `PUT /rents/` całkowicie zablokowany. Każda próba modyfikacji (niezależnie od struktury JSON) kończy się błędem 500 Internal Server Error.
* **ST-04.05 (Pozytywny): Zwrot książki / Usunięcie wypożyczenia (DELETE)** *(Postman request: `ST-04.05 Positive: Delete rental from history`)*
* * *Opis:* Usunięcie lub zamknięcie rekordu wypożyczenia z bazy.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED** (Serwer poprawnie zwrócił wartość `true`).
  * *Opis:* Usunięcie lub zamknięcie rekordu wypożyczenia z bazy.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED** (Serwer poprawnie zwrócił wartość `true`).
