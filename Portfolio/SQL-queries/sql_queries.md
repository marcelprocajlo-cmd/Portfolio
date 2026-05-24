PRAKTYCZNE PRZYPADKI TESTOWE SQL (GOTOWE DO REPRODUKCJI)
================================================================================

--------------------------------------------------------------------------------
IDENTYFIKATOR: SQL-TC-01: Test usuwania kaskadowego (Integracja i FK)
--------------------------------------------------------------------------------
* Moduł:           Zarządzanie kontami i zamówieniami
* Opis:            Sprawdzenie, czy usunięcie użytkownika poprawnie czyści jego 
                   zamówienia (brak osieroconych rekordów w bazie).

[SKRYPT PRZYGOTOWAWCZY (MOCK DATA)]
CREATE TABLE test_users (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE test_orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    product VARCHAR(50),
    FOREIGN KEY (user_id) REFERENCES test_users(id) ON DELETE CASCADE
);

INSERT INTO test_users VALUES (1, 'Jan Kowalski');
INSERT INTO test_orders VALUES (100, 1, 'Laptop'), (101, 1, 'Myszka');

[KROKI TESTOWE]
1. Usuń użytkownika z tabeli nadrzędnej:
   DELETE FROM test_users WHERE id = 1;

2. Sprawdź, czy zamówienia powiązane z tym użytkownikiem również zniknęły:
   SELECT COUNT(*) AS pozostałe_zamówienia FROM test_orders WHERE user_id = 1;

[OCZEKIWANY REZULTAT]
Zapytanie sprawdzające (Krok 2) zwraca wynik `0`. 
Baza danych automatycznie usunęła rekordy z tabeli `test_orders` dzięki regule 
ON DELETE CASCADE.



--------------------------------------------------------------------------------
IDENTYFIKATOR: SQL-TC-02: Walidacja transakcyjności przy błędzie (Rollback)
--------------------------------------------------------------------------------
* Moduł:           Finanse i Portfel
* Opis:            Symulacja sytuacji, w której podczas transferu środków między 
                   kontami, drugi krok kończy się błędem. Baza musi cofnąć wszystko.

[SKRYPT PRZYGOTOWAWCZY (MOCK DATA)]
CREATE TABLE test_wallets (
    user_id INT PRIMARY KEY,
    balance DECIMAL(10,2)
);

INSERT INTO test_wallets VALUES (1, 100.00), (2, 50.00);

[KROKI TESTOWE]
-- UWAGA: Jeśli Twoje środowisko (np. W3Schools) nie obsługuje komend BEGIN/ROLLBACK,
-- wykonaj poniższy krok, aby zobaczyć jak baza danych REAGUJE na błędy składni:

1. Spróbuj wykonać zapytanie z błędem w nazwie kolumny:
   UPDATE test_wallets SET balance = balance + 50.00, nieistnieje = 1 WHERE user_id = 2;

2. Sprawdź, czy baza danych zmieniła jakikolwiek rekord:
   SELECT * FROM test_wallets;

[OCZEKIWANY REZULTAT]
* W Kroku 1 baza danych zwraca błąd (Error: no such column: nieistnieje).
* W Kroku 2 stan kont wynosi: User 1 = 100.00, User 2 = 50.00. 
* Ponieważ zapytanie zawierało błąd, baza danych nie miała prawa zmienić ani jednego grosza na koncie Użytkownika 2. Suma pieniędzy w systemie (150.00) pozostała bez zmian.

--------------------------------------------------------------------------------
IDENTYFIKATOR: SQL-TC-03: Walidacja duplikatów (Unique Constraint)
--------------------------------------------------------------------------------
* Moduł:           Rejestracja użytkowników
* Opis:            Sprawdzenie, czy baza danych twardo blokuje próby rejestracji 
                   dwóch osób na ten sam adres e-mail.

[SKRYPT PRZYGOTOWAWCZY (MOCK DATA)]
CREATE TABLE test_registration (
    id INT AUTO_INCREMENT PRIMARY KEY, -- lub SERIAL PRIMARY KEY dla PostgreSQL
    email VARCHAR(100) UNIQUE,
    username VARCHAR(50)
);

INSERT INTO test_registration (email, username) VALUES ('test@qa.pl', 'Tester1');

[KROKI TESTOWE]
1. Spróbuj wstrzyknąć do bazy drugiego użytkownika z dokładnie tym samym mailem:
   INSERT INTO test_registration (email, username) VALUES ('test@qa.pl', 'Tester2');

[OCZEKIWANY REZULTAT]
Baza danych zwraca błąd walidacji (np. `Duplicate entry 'test@qa.pl' for key 'email'` 
lub `id_klucza_unique violation`). Rekord nie zostaje zapisany w bazie. 
Zapytanie `SELECT COUNT(*) FROM test_registration;` zwraca wynik `1`.
