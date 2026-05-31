# Projekt Końcowy – Scenariusz i Testy API Systemu E-book Rental

---

## Wykaz Scenariuszy Testowych & Raport z Wykonania (Test Execution Report)

###  MODUŁ 1: Zarządzanie Użytkownikami (`/users/`)
* **ST-01.01 (Pozytywny): Rejestracja nowego użytkownika (POST)**
  * *Opis:* Dodanie nowego czytelnika do bazy danych.
  * *Oczekiwany rezultat:* Status 200 OK, serwer zwraca unikalny identyfikator użytkownika (`userId`).
  * *Status:* **PASSED** (Uzyskano ID: 6194)
* **ST-01.02 (Pozytywny): Pobranie listy użytkowników (GET)**
  * *Opis:* Wyświetlenie wszystkich zarejestrowanych osób.
  * *Oczekiwany rezultat:* Status 200 OK, lista obiektów użytkowników.
  * *Status:* **PASSED**

###  MODUŁ 2: Katalog Książek (`/books/`)
* **ST-02.01 (Pozytywny): Dodanie nowej książki do katalogu (POST)**
  * *Opis:* Utworzenie nowego tytułu (metadanych książki) w systemie.
  * *Oczekiwany rezultat:* Status 200 OK, serwer zwraca ID książki.
  * *Status:* **PASSED** (Uzyskano ID: 6198)
* **ST-02.02 (Pozytywny): Pobranie całego katalogu książek (GET)**
  * *Opis:* Wyświetlenie pełnej listy tytułów dostępnych w bibliotece.
  * *Oczekiwany rezultat:* Status 200 OK, lista obiektów JSON zawierająca dodane pozycje.
  * *Status:* **PASSED**

###  MODUŁ 3: Zarządzanie Egzemplarzami (`/items/`)
* **ST-03.01 (Pozytywny): Pobranie listy egzemplarzy dla danej książki (GET)**
  * *Opis:* Sprawdzenie fizycznych kopii przypisanych do wybranego tytułu.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED**
* **ST-03.02 (Pozytywny): Dodanie nowego egzemplarza książki (POST)**
  * *Opis:* Wprowadzenie do bazy nowej fizycznej sztuki książki o statusie "dostępna".
  * *Oczekiwany rezultat:* Status 201 Created, automatyczne nadanie daty zakupu.
  * *Status:* **FAILED (Wykryto Bug)** – System zwraca błąd 500, dopóki nie wymusi się ręcznego wpisania `purchaseDate` w ciele JSON. Po obejściu błędu uzyskano status 200 OK (ID egzemplarza: 6199).
* **ST-03.03 (Pozytywny): Usunięcie egzemplarza z systemu (DELETE)**
  * *Opis:* Skasowanie konkretnej kopii książki przy użyciu jej identyfikatora.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED**

###  MODUŁ 4: Operacje Wypożyczeń (`/rents/`)
* **ST-04.01 (Pozytywny): Pobranie listy transakcji dla nowej kopii (GET)**
  * *Opis:* Wyświetlenie historii wypożyczeń przypisanych do `itemId`.
  * *Oczekiwany rezultat:* Status 200 OK, pusta lista `[]` dla nowego egzemplarza.
  * *Status:* **PASSED**
* **ST-04.02 (Pozytywny): Nowe wypożyczenie – weryfikacja automatycznych dat (POST)**
  * *Opis:* Tworzenie transakcji wypożyczenia dla klienta.
  * *Oczekiwany rezultat:* Status 201 Created, automatyczne wyliczenie daty zwrotu.
  * *Status:* **FAILED (Wykryto Bug)** – Backend nie generuje dat automatycznie (błąd 500). Dopiero po ręcznym dopisaniu pola `"rentDate": "2026-05-31"` system przepuścił żądanie (ID wypożyczenia: 6201).
* **ST-04.03 (Negatywny): Próba wypożyczenia niedostępnej książki (POST)**
  * *Opis:* Próba ponownego wypożyczenia egzemplarza, który jest już aktualnie zajęty przez innego klienta.
  * *Oczekiwany rezultat:* Status 400 Bad Request lub 409 Conflict. Blokada operacji.
  * *Status:* **FAILED (Krytyczny błąd logiczny)** – System zwrócił status 200 OK (ID: 6202) i pozwolił dwóm różnym klientom wypożyczyć tę samą fizyczną książkę w tym samym czasie. Brak walidacji statusu dostępności.
* **ST-04.04 (Pozytywny): Edycja trwającego wypożyczenia (PUT)**
  * *Opis:* Zmiana danych lub przedłużenie terminu zwrotu w aktywnej transakcji.
  * *Oczekiwany rezultat:* Status 200 OK i zaktualizowany obiekt.
  * *Status:* **FAILED (Krytyczny Bug)** – Endpoint `PUT /rents/` całkowicie zablokowany. Każda próba modyfikacji (niezależnie od struktury JSON) kończy się błędem 500 Internal Server Error.
* **ST-04.05 (Pozytywny): Zwrot książki / Usunięcie wypożyczenia (DELETE)**
  * *Opis:* Usunięcie lub zamknięcie rekordu wypożyczenia z bazy.
  * *Oczekiwany rezultat:* Status 200 OK.
  * *Status:* **PASSED** (Serwer poprawnie zwrócił wartość `true`).