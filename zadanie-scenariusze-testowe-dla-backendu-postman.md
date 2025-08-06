# Scenariusze testowe backendu – wypożyczalnia eBooków

## 1. Logowanie / Rejestracja użytkownika

| Nr | Test                                   | Endpoint             | Metoda | Body (przykład)                                                                                   | Oczekiwany rezultat               |
|----|----------------------------------------|----------------------|--------|---------------------------------------------------------------------------------------------------|-----------------------------------|
| 1  | Rejestracja nowego użytkownika         | /users/register      | POST   | `{ "username": "jan.kowalski", "password": "Test123!", "email": "jan@kowalski.pl" }`             | 201 Created, dane użytkownika     |
| 2  | Rejestracja z brakującymi polami       | /users/register      | POST   | `{ "username": "jan.kowalski" }`                                                                  | 400 Bad Request, błąd walidacji   |
| 3  | Logowanie poprawne                     | /users/login         | POST   | `{ "username": "jan.kowalski", "password": "Test123!" }`                                          | 200 OK, token JWT                 |
| 4  | Logowanie złe hasło                    | /users/login         | POST   | `{ "username": "jan.kowalski", "password": "ZłeHasło!" }`                                         | 401 Unauthorized                  |

---

## 2. Lista tytułów

| Nr | Test                       | Endpoint            | Metoda | Body / URL parametry (przykład)                                                               | Oczekiwany rezultat      |
|----|----------------------------|---------------------|--------|-----------------------------------------------------------------------------------------------|--------------------------|
| 1  | Pobranie listy tytułów     | /books              | GET    | Header: `Authorization: Bearer {token}`                                                       | 200 OK, lista książek    |
| 2  | Dodanie nowego tytułu      | /books              | POST   | `{ "author": "Adam Mickiewicz", "title": "Pan Tadeusz", "year": 1834 }`                       | 201 Created, nowa książka|
| 3  | Edycja istniejącego tytułu | /books/{id}         | PUT    | `{ "author": "Adam Mickiewicz", "title": "Dziady", "year": 1823 }`                            | 200 OK, zaktualizowane dane|
| 4  | Usunięcie tytułu           | /books/{id}         | DELETE | (id=1) URL: `/books/1`                                                                        | 204 No Content           |

---

## 3. Lista egzemplarzy

| Nr | Test                       | Endpoint                 | Metoda | Body / URL parametry (przykład)                                                        | Oczekiwany rezultat         |
|----|----------------------------|--------------------------|--------|----------------------------------------------------------------------------------------|-----------------------------|
| 1  | Pobranie egzemplarzy książki| /books/{id}/copies       | GET    | (id=1) URL: `/books/1/copies`                                                          | 200 OK, lista egzemplarzy   |
| 2  | Dodanie egzemplarza         | /books/{id}/copies       | POST   | `{ "purchaseDate": "2024-01-05", "status": "AVAILABLE" }`                              | 201 Created, nowy egzemplarz|
| 3  | Edycja egzemplarza          | /copies/{id}             | PUT    | `{ "purchaseDate": "2024-01-05", "status": "RENTED" }`                                 | 200 OK, zaktualizowany egz. |
| 4  | Usunięcie egzemplarza       | /copies/{id}             | DELETE | (id=10) URL: `/copies/10`                                                              | 204 No Content              |

---

## 4. Lista wypożyczeń

| Nr | Test                           | Endpoint                      | Metoda | Body / URL parametry (przykład)                                               | Oczekiwany rezultat      |
|----|--------------------------------|-------------------------------|--------|-------------------------------------------------------------------------------|--------------------------|
| 1  | Pobranie wypożyczeń egzemplarza| /copies/{id}/loans            | GET    | (id=10) URL: `/copies/10/loans`                                               | 200 OK, lista wypożyczeń |
| 2  | Dodanie wypożyczenia           | /copies/{id}/loans            | POST   | `{ "clientName": "Anna Nowak", "startDate": "2024-08-01", "endDate": "2024-08-04" }` | 201 Created, nowe wypożyczenie|
| 3  | Edycja wypożyczenia            | /loans/{id}                   | PUT    | `{ "clientName": "Anna Nowak", "startDate": "2024-08-02", "endDate": "2024-08-08" }` | 200 OK, zaktualizowane wypożyczenie|
| 4  | Usunięcie wypożyczenia         | /loans/{id}                   | DELETE | (id=5) URL: `/loans/5`                                                        | 204 No Content           |

---

## 5. Autoryzacja i dostęp

| Nr | Test                       | Endpoint               | Metoda | Body / URL parametry (przykład) | Oczekiwany rezultat    |
|----|----------------------------|------------------------|--------|----------------------------------|------------------------|
| 1  | Dostęp bez tokena          | /books                 | GET    | Brak nagłówka Authorization      | 401 Unauthorized       |
| 2  | Dostęp z niepoprawnym tokenem | /books               | GET    | Header: `Authorization: Bearer FAKE_TOKEN` | 401 Unauthorized       |

---

## Uwagi ogólne:
- W requestach wymagających autoryzacji należy przekazać nagłówek `Authorization: Bearer {token}` (token otrzymywany po zalogowaniu).
- Warto testować również przypadki brzegowe, np. edycję/usuwanie nieistniejącego rekordu (`404 Not Found`).
- W body requestów używaj rzeczywistych, przykładowych danych – pozwoli to łatwo wykonać test w Postmanie.