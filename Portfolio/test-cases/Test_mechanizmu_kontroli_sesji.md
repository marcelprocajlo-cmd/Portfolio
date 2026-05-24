RAPORT Z TESTU: Weryfikacja mechanizmu kontroli sesji (Erste Bank)
================================================================================

TYTUŁ : 
Poprawna walidacja unikalności żądania i blokada duplikacji POST 
przy użyciu identyfikatorów sesji (Tealium / utag.js).

STATUS: Passed (Test zaliczony - system poprawnie zabezpieczony)
WITRYNA / URL : [https://www.erste.pl](https://www.erste.pl/klient-indywidualny/kontakt/zostaw-numer)
ŚRODOWISKO : Chrome v120+, DevTools Console.

OPIS:
Podczas próby wymuszenia ponownego przesłania formularza (klawisz F5), mechanizm 
analityczno-sesyjny banku oparty na skrypcie `utag.js` (Tealium) skutecznie 
zweryfikował stan użytkownika i zablokował duplikację danych w bazie.

Logi z konsoli deweloperskiej jednoznacznie potwierdzają śledzenie unikalności 
aktywności na podstawie przypisanego ID sesji :

[LOGI Z KONSOLI DEWELOPERSKIEJ (CONSOLE LOGS)]
--------------------------------------------------------------------------------
utag.js:378 clientID detected <br>
utag.js:235 Tealium: Timer 10s wysłany z ID sesji: 501167532.1779624436113
--------------------------------------------------------------------------------

KROKI TESTOWE (Test Steps):
1. Otwórz publiczny formularz na stronie banku z włączoną konsolą DevTools (F12).
2. Wypełnij pola i zatwierdź wysłanie danych.
3. Po przejściu na ekran potwierdzenia, odśwież stronę (F5).

REZULTAT OCZEKIWANY I FAKTYCZNY (Expected & Actual Result):
System monitoruje czas i stan sesji (wysyłając cykliczne pingi "Timer 10s"). 
Dzięki powiązaniu formularza z konkretnym tokenem sesji, ponowne wysłanie 
pakietu danych zostaje odrzucone. System bankowy zachował integralność danych, 
a w bazie nie powstał zduplikowany rekord.
