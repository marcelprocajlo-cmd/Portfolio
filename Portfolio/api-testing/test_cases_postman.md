
 SZCZEGÓŁOWE PRZYPADKI TESTOWE – API (POSTMAN)
================================================================================

--------------------------------------------------------------------------------
IDENTYFIKATOR: API-TC-01: Logowanie zepsutym JSON-em (Malformed JSON Payload)
--------------------------------------------------------------------------------
* Moduł:           Autoryzacja i Zarządzanie Sesją
* API / Endpoint:  POST https://restful-booker.herokuapp.com/auth
* Rodzaj testu:    Test bezpieczeństwa, Walidacja negatywna, Edge Case

[WARUNKI WSTĘPNE]
1. Zainstalowany program Postman.
2. Utworzony nowy request typu POST z adresem endpointu.
3. W zakładce Headers ustawiono klucz Content-Type na wartość application/json.

[KROKI TESTOWE]
1. Przejdź do zakładki Body, wybierz opcję raw oraz format JSON.
2. Wklej celowo uszkodzony strukturalnie obiekt JSON:
   {
       "username" : "admin"
       "password" : "password123
   }
3. Kliknij przycisk Send.

[OCZEKIWANY REZULTAT]
* Status Code: 400 Bad Request.
* Response Body: Serwer nie powinien wygenerować tokenu autoryzacyjnego. Zamiast 
  tego powinien zwrócić czytelny komunikat o błędzie parsowania danych, 
  a aplikacja nie może wyrzucić błędu serwera typu 500 Internal Server Error.

--------------------------------------------------------------------------------
IDENTYFIKATOR: API-TC-02: Wstrzykiwanie tablicy zamiast stringa (Array Injection)
--------------------------------------------------------------------------------
* Moduł:           Tworzenie Zasobów i Walidacja Typów Danych
* API / Endpoint:  POST https://jsonplaceholder.typicode.com/posts
* Rodzaj testu:    Test walidacji typów (Type Spoofing), Boundary Case

[WARUNKI WSTĘPNE]
1. Przygotowany request POST do tworzenia nowego wpisu.
2. Nagłówek Content-Type ustawiony na application/json.

[KROKI TESTOWE]
1. Przejdź do zakładki Body -> raw -> JSON.
2. Przygotuj obiekt, w którym zamiast tekstu w polu title, przekażesz tablicę:
   {
       "title": [100, 200, 300, 400],
       "body": "Testowanie odporności API na niepoprawne typy danych.",
       "userId": 1
   }
3. Kliknij przycisk Send.

[OCZEKIWANY REZULTAT]
* Status Code: 400 Bad Request (lub automatyczna konwersja na ciąg znaków).
* Bezpieczeństwo: Jeśli API zwróci status 201 Created, baza danych musi obsłużyć 
  to żądanie bezpiecznie, nie powodując awarii backendu przy próbie zapisu.

--------------------------------------------------------------------------------
IDENTYFIKATOR: API-TC-03: Poszukiwanie użytkownika z ID z kosmosu
--------------------------------------------------------------------------------
* Moduł:           Pobieranie Danych i Identyfikatory
* API / Endpoint:  GET https://reqres.in/api/users/
* Rodzaj testu:    Test parametrów ścieżki (Path Parameter), SQL Injection

[WARUNKI WSTĘPNE]
1. Przygotowany request typu GET w programie Postman.

[KROKI TESTOWE]
1. Wpisz adres endpointu, dodając na końcu ID ze znakami specjalnymi:
   https://reqres.in/api/users/99999999' OR '1'='1
2. Kliknij przycisk Send.
3. Krok alternatywny: Zmień ID na wartość tekstową:
   https://reqres.in/api/users/niedozwolony_string
4. Kliknij przycisk Send.

[OCZEKIWANY REZULTAT]
* Status Code: W obu przypadkach system powinien zwrócić kod 404 Not Found.
* Response Body: Odpowiedź pusta {} lub komunikat typu {"error": "User not found"}. 
  Serwer pod żadnym pozorem nie może ujawnić struktury bazy danych (stack trace) 
  ani zwrócić danych innych użytkowników.
================================================================================
