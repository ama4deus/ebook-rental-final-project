# Projekt Końcowy: Scenariusze i Testy API Systemu E-book Rental

## 1. Wstęp i Opis Projektu
Niniejsza dokumentacja przedstawia kompletny proces testowy dla backendowej części aplikacji **E-book Rental** – systemu zarządzania wypożyczalnią e-booków. Backend aplikacji udostępnia architekturę opartą o kontrolery REST API. 

**Cel testów:** Weryfikacja poprawności działania kluczowych operacji biznesowych (rejestracja, katalogowanie, zarządzanie zasobami oraz transakcje wypożyczeń) oraz odporności systemu na podstawowe błędy walidacji.

### Metodologia i Narzędzia:
* **Narzędzie główne:** Postman (przygotowanie zautomatyzowanej kolekcji żądań HTTP).
* **Zakres testów:** 16 scenariuszy testowych (zarówno pozytywnych typu *Happy Path*, jak i negatywnych sprawdzających walidację).
* **Środowisko testowe:** Replit (`https://ta-ebookrental-be-kodillaedu.replit.app`)

### Metryki Wykonania Testów:
* **Liczba rozpisanych scenariuszy:** 16
* **Scenariusze zakończone powodzeniem (PASSED):** 11
* **Scenariusze zakończone niepowodzeniem (FAILED):** 5
* **Łączna liczba zidentyfikowanych defektów:** 4 (w tym 2 o priorytecie Krytycznym).

---

## 2. Wykaz Scenariuszy Testowych & Raport z Wykonania

### MODUŁ 1: Zarządzanie Użytkownikami (`/user/register`, `/user/login`)
* **ST-01.01 (Pozytywny): Rejestracja nowego użytkownika (POST /user/register)**
    * *Opis:* Dodanie nowego czytelnika do bazy danych.
    * *Oczekiwany rezultat:* Status 200 OK, serwer zwraca unikalny identyfikator użytkownika (`userId`).
    * *Status:* **PASSED** (Uzyskano ID: 6194)
* **ST-01.02 (Negatywny): Rejestracja istniejącego użytkownika (POST /user/register)**
    * *Opis:* Próba rejestracji na zajęty login.
    * *Oczekiwany rezultat:* Status 400 Bad Request lub komunikat o konflikcie.
    * *Status:* **PASSED**
* **ST-01.03 (Pozytywny): Poprawne logowanie użytkownika (POST /user/login)**
    * *Opis:* Logowanie do systemu przy użyciu poprawnych danych uwierzytelniających.
    * *Oczekiwany rezultat:* Status 200 OK, potwierdzenie autoryzacji.
    * *Status:* **PASSED**
* **ST-01.04 (Negatywny): Logowanie z niepoprawnym hasłem (POST /user/login)**
    * *Opis:* Próba logowania z błędnymi danymi.
    * *Oczekiwany rezultat:* Status 401 Unauthorized lub 400 Bad Request.
    * *Status:* **PASSED**

### MODUŁ 2: Katalog Książek (`/titles/`)
* **ST-02.01 (Pozytywny): Pobranie całego katalogu książek (GET /titles/)**
    * *Opis:* Wyświetlenie pełnej listy tytułów dostępnych w bibliotece.
    * *Oczekiwany rezultat:* Status 200 OK, lista obiektów JSON zawierająca dodane pozycje.
    * *Status:* **PASSED**
* **ST-02.02 (Pozytywny): Dodanie nowej książki do katalogu (POST /titles/)**
    * *Opis:* Utworzenie nowego tytułu (metadanych książki) w systemie.
    * *Oczekiwany rezultat:* Status 200 OK, serwer zwraca ID książki.
    * *Status:* **PASSED** (Uzyskano ID: 6198)
* **ST-02.03 (Negatywny): Walidacja pól obowiązkowych przy dodawaniu książki (POST /titles/)**
    * *Opis:* Próba dodania książki z pustym polem "author".
    * *Oczekiwany rezultat:* Status 400 Bad Request.
    * *Status:* **PASSED**
* **ST-02.04 (Pozytywny): Aktualizacja danych książki (PUT /titles/)**
    * *Opis:* Zmiana metadanych (np. roku wydania lub tytułu) istniejącej książki.
    * *Oczekiwany rezultat:* Status 200 OK.
    * *Status:* **PASSED**
* **ST-02.05 (Pozytywny): Usunięcie książki z katalogu (DELETE /titles/)**
    * *Opis:* Skasowanie konkretnej pozycji książkowej za pomocą ID.
    * *Oczekiwany rezultat:* Status 200 OK.
    * *Status:* **PASSED**

### MODUŁ 3: Zarządzanie Egzemplarzami (`/items/`)
* **ST-03.01 (Pozytywny): Pobranie listy egzemplarzy dla danej książki (GET /items/)**
    * *Opis:* Sprawdzenie fizycznych kopii przypisanych do wybranego tytułu.
    * *Oczekiwany rezultat:* Status 200 OK.
    * *Status:* **PASSED**
* **ST-03.02 (Pozytywny): Dodanie nowego egzemplarza książki (POST /items/)**
    * *Opis:* Wprowadzenie do bazy nowej fizycznej sztuki książki o statusie "dostępna".
    * *Oczekiwany rezultat:* Status 201 Created, automatyczne nadanie daty zakupu przez system.
    * *Status:* **FAILED** (Wykryto BUG-01)
* **ST-03.03 (Pozytywny): Modyfikacja szczegółów egzemplarza (PUT /items/)**
    * *Opis:* Zmiana danych fizycznego egzemplarza.
    * *Oczekiwany rezultat:* Status 200 OK.
    * *Status:* **PASSED**
* **ST-03.04 (Pozytywny): Usunięcie egzemplarza z systemu (DELETE /items/)**
    * *Opis:* Skasowanie konkretnej kopii książki przy użyciu jej identyfikatora.
    * *Oczekiwany rezultat:* Status 200 OK.
    * *Status:* **PASSED**

### MODUŁ 4: Operacje Wypożyczeń (`/rents/`)
* **ST-04.01 (Pozytywny): Pobranie listy transakcji dla nowej kopii (GET /rents/)**
    * *Opis:* Wyświetlenie historii wypożyczeń przypisanych do `itemId`.
    * *Oczekiwany rezultat:* Status 200 OK, pusta lista `[]` dla nowego egzemplarza.
    * *Status:* **PASSED**
* **ST-04.02 (Pozytywny): Nowe wypożyczenie (POST /rents/)**
    * *Opis:* Tworzenie transakcji wypożyczenia dla klienta.
    * *Oczekiwany rezultat:* Status 201 Created, automatyczne wyliczenie daty zwrotu.
    * *Status:* **FAILED** (Wykryto BUG-02)
* **ST-04.03 (Negatywny): Próba wypożyczenia niedostępnej książki (POST /rents/)**
    * *Opis:* Próba ponownego wypożyczenia egzemplarza, który jest już aktualnie zajęty przez innego klienta.
    * *Oczekiwany rezultat:* Status 400 Bad Request lub 409 Conflict. Blokada operacji.
    * *Status:* **FAILED** (Wykryto BUG-03 – Krytyczny błąd logiczny)
* **ST-04.04 (Pozytywny): Edycja trwającego wypożyczenia (PUT /rents/)**
    * *Opis:* Zmiana danych lub przedłużenie terminu zwrotu w aktywnej transakcji.
    * *Oczekiwany rezultat:* Status 200 OK i zaktualizowany obiekt.
    * *Status:* **FAILED** (Wykryto BUG-04 – Krytyczna blokada funkcjonalności)
* **ST-04.05 (Pozytywny): Zwrot książki / Usunięcie wypożyczenia (DELETE /rents/)**
    * *Opis:* Usunięcie lub zamknięcie rekordu wypożyczenia z bazy.
    * *Oczekiwany rezultat:* Status 200 OK (serwer zwraca `true`).
    * *Status:* **PASSED**

---

## 3. Znalezione Błędy (Bug Report)

Podczas sesji testowej wykryto następujące nieprawidłowości w działaniu API:

| Identyfikator | Priorytet | Endpoint | Krótki Opis Defektu |
| :--- | :--- | :--- | :--- |
| **BUG-01** | Poważny | `POST /items/` | System zwraca błąd `500 Internal Server Error` przy próbie dodania egzemplarza bez ręcznego zdefiniowania pola `purchaseDate`. Data zakupu powinna być opcjonalna lub generowana po stronie serwera. |
| **BUG-02** | Poważny | `POST /rents/` | Backend nie generuje dat automatycznie. Wysłanie żądania bez pola `rentDate` kończy się błędem systemowym `500`. Serwer wymusza przesyłanie daty w ciele JSON. |
| **BUG-03** | Krytyczny | `POST /rents/` | **Brak walidacji biznesowej (Overbooking).** System pozwala na jednoczesne wypożyczenie tego samego fizycznego egzemplarza (`itemId`) przez dwóch różnych klientów w tym samym czasie, zwracając status `200 OK`. |
| **BUG-04** | Krytyczny | `PUT /rents/` | **Całkowita blokada funkcjonalności.** Każda próba modyfikacji aktywnego wypożyczenia (metoda PUT), niezależnie od poprawności struktury dokumentu JSON, kończy się błędem `500 Internal Server Error`. |
