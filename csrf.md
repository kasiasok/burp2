1. usunac csrf (poc csrf in browser) <br>
2. zmienic post>get + usunac csrf (poc csrf in browser) <br>
3. get + csrfpoc head <meta name="referrer" content="no-referrer"> (poc csrf in browser) <br>


<hr>
2. Walidacja Referer/Origin (obrona pomocnicza)
Serwer sprawdza, czy nagłówek Referer (lub lepiej: Origin, bo jest bardziej niezawodny) wskazuje na własną domenę. Jeśli żądanie przychodzi z attacker.com, serwer je odrzuca.
<br>
LAX: <br>
Żądanie jest metodą GET <br>
Jest to top-level navigation — czyli użytkownik faktycznie zmienia adres w pasku przeglądarki (klika link, wpisuje URL, przechodzi przez <a href>, przekierowanie).

<br>
formularze POC CSRF ida przez POST. 

<hr>
😈 Strona atakującego</h1>
  <p>Wygląda niewinnie (np. "zabawny quiz" wysłany ofierze).</p>
  <p>W tle, zaraz po załadowaniu, formularz zostanie automatycznie
     wysłany metodą <b>GET</b> do <code>change_email.php</code>
     na docelowej aplikacji — bez tokena CSRF i bez nagłówka Referer.</p>

<hr>
 Najlepiej: token CSRF + `SameSite=Strict/Lax` na cookie sesyjnym jako
zamiast polegać na samym Referer.
