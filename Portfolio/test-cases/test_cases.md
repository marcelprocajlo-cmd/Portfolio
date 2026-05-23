## 📋 Szczegółowe Przypadki Testowe

### 1. Moduł: Koszyk zakupowy i Płatności (E-commerce)
*   **Platforma testowa:** Automation Exercise (`automationexercise.com`)
*   **Rodzaj testu:** Test funkcjonalny, Obsługa błędów, Edge Case
*   **Identyfikator:** `TC-01: Atak „Szybkiego Palca” (Double-click attack)`

| Sekcja | Treść |
| :--- | :--- |
| **Warunki wstępne** | 1. Użytkownik jest zalogowany na konto testowe. <br> 2. W koszyku znajduje się minimum jeden produkt. <br> 3. Użytkownik przeszedł do ostatniego kroku płatności i uzupełnił dane karty testowej. |
| **Kroki testowe** | 1. Zlokalizuj przycisk finalizujący zamówienie (np. *„Pay and Confirm Order”*). <br> 2. Wykonaj bardzo szybkie, potrójne kliknięcie (triple-click) lewym przyciskiem myszy w przycisk (symulacja niecierpliwego użytkownika). <br> 3. Odczekaj na przekierowanie do ekranu sukcesu. <br> 4. Przejdź do zakładki *„Order History”* (Historia zamówień) na profilu użytkownika. |
| **Oczekiwany rezultat** | System po pierwszym kliknięciu powinien natychmiast zablokować przycisk (status *disabled* lub nałożenie loader'a), uniemożliwiając wysłanie kolejnych żądań API. W historii zamówień powinno pojawić się **tylko jedno** wygenerowane zamówienie. Kwota z konta powinna zostać pobrana tylko raz. |

---

### 2. Moduł: Formularze i Integracja z Bazą Danych (Hotel Booking)
*   **Platforma testowa:** Restful-Booker-Platform (`automationintesting.online`)
*   **Rodzaj testu:** Test walidacji danych, Test UI/UX, Edge Case
*   **Identyfikator:** `TC-02: Kopiuj-Wklej w ekstremalnych przypadkach `

| Sekcja | Treść |
| :--- | :--- |
| **Warunki wstępne** | 1. Użytkownik znajduje się na stronie głównej z widocznym formularzem kontaktowym (*„Contact”*). |
| **Kroki testowe** | 1. W polu *„Name”*, *„Email”* oraz *„Phone”* wpisz poprawne, standardowe dane testowe. <br> 2. W polu *„Subject”* wklej ciąg znaków specjalnych oraz emotikonów: `Test!@#$%^&*()_+ 🙂🎁✈️🔥`. <br> 3. W polu *„Description”* wklej wygenerowany tekst (np. Lorem Ipsum) o długości dokładnie **5000 znaków**. <br> 4. Kliknij przycisk *„Submit”*. |
| **Oczekiwany rezultat** | **Dla pola Subject:** System poprawnie zapisuje i wyświetla emotikony oraz znaki specjalne (brak błędów kodowania UTF-8, brak tzw. „krzaczków”). <br> **Dla pola Description:** System albo nie pozwala wkleić więcej niż wynosi limit (blokada na poziomie UI), albo wyświetla czytelny komunikat walidacyjny (np. *„Wiadomość może mieć maksymalnie 1000 znaków”*). Aplikacja nie rzuca błędem bazy danych (np. *SQL internal error*) o przepełnieniu bufora. |

---

### 3. Moduł: Obsługa Asynchroniczności i Stabilność UI (Dynamic Features)
*   **Platforma testowa:** The Internet Herokuapp (`the-internet.herokuapp.com/dynamic_loading`)
*   **Rodzaj testu:** Test sieciowy, Testy regresji UI, Edge Case
*   **Identyfikator:** `TC-03: Testy Słabego Ogniwa (Network Throttling & Offline)`

| Sekcja | Treść |
| :--- | :--- |
| **Warunki wstępne** | 1. Użytkownik znajduje się na podstronie *„Dynamic Loading -> Example 1”*. <br> 2. Otworzone są narzędzia deweloperskie przeglądarki (Chrome DevTools) na zakładce **Network**. |
| **Kroki testowe** | 1. Kliknij przycisk *„Start”*, aby zainicjować asynchroniczne ładowanie elementu (pojawi się animowany loader). <br> 2. W trakcie gdy loader się kręci, w DevTools zmień profil sieciowy z „No throttling” na **„Offline”** (symulacja nagłego zerwania połączenia Wi-Fi). <br> 3. Obserwuj zachowanie interfejsu użytkownika przez 30 sekund. |
| **Oczekiwany rezultat** | Aplikacja nie powinna zawiesić się w nieskończonej pętli ładowania (loader nie może kręcić się bez końca). Po upłynięciu limitu czasu żądania (timeout), na ekranie powinien pojawić się czytelny dla użytkownika komunikat o błędu sieciowym (np. *„Błąd połączenia. Sprawdź swój internet i spróbuj ponownie”*), a interfejs powinien umożliwić ponowne kliknięcie przycisku po odzyskaniu sieci. |
