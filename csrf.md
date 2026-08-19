TESTOWY EMAIL
1. usunac csrf (poc csrf in browser) <br>
2. zmienic post>get + usunac csrf (poc csrf in browser) <br>
3. get + usunac csrf + csrfpoc: head ``` <meta name="referrer" content="no-referrer"> ```(poc csrf in browser) <br> 

DOCELOWY EMAIL
wkleic dzialajacy w csrf poc w EXPLOIT BODY i deliver to the victim!!!



<hr>
2. Walidacja Referer/Origin (obrona pomocnicza)
Serwer sprawdza, czy nagłówek Referer (lub lepiej: Origin, bo jest bardziej niezawodny) wskazuje na własną domenę. Jeśli żądanie przychodzi z attacker.com, serwer je odrzuca.
<br>
LAX: <br>
Żądanie jest metodą GET <br>
Jest to top-level navigation — czyli użytkownik faktycznie zmienia adres w pasku przeglądarki (klika link, wpisuje URL, przechodzi przez a href, przekierowanie).
<hr>
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


<hr>
potencjalny atak <br>
Jeśli ofiara nie jest zalogowana w banku w momencie otwarcia quizu, przeglądarka nie ma czego dołączyć — żądanie leci bez ciasteczka sesji, bank odrzuca jako niezalogowane.<br>

Atakujący rozsyła jeden link do tysięcy potencjalnych ofiar (SMS, mail, reklama, złośliwa strona SEO).
Jeden statyczny kod, identyczny dla wszystkich ofiar.<br>

to przeglądarka ofiary dokleja ciasteczko sesji automatycznie — atakujący tylko konstruuje żądanie, a przeglądarka robi resztę.<br>


<hr>

Skąd bierze się "personalizacja" ataku, jeśli kod jest statyczny

To, co sprawia że atak "działa na konkretną ofiarę", dzieje się nie w kodzie atakującego, tylko w przeglądarce ofiary, w momencie wysyłki żądania:

Przeglądarka ofiary ma zapisane w swoim magazynie ciasteczek: session=abc123xyz dla domeny pkobp.pl (bo ofiara jest tam zalogowana).
Gdy JavaScript na stronie atakującego wywołuje form.submit() do pkobp.pl/przelew, przeglądarka automatycznie dołącza to konkretne ciasteczko sesji ofiary do żądania — bo tak działa mechanizm ciasteczek w ogóle (przeglądarka wysyła wszystkie ciasteczka pasujące do domeny docelowej, niezależnie skąd request pochodzi).
Bank widzi żądanie z ważną sesją abc123xyz i wykonuje przelew w imieniu tej konkretnej ofiary, mimo że atakujący nigdy nie widział ani nie znał tego tokena.

<hr>

<img width="687" height="511" alt="image" src="https://github.com/user-attachments/assets/9e694744-8cdf-4940-8dae-136664183939" />

Dlaczego akurat ten formularz, a nie np. fetch()

Zwróć uwagę, że w tym przykładzie atakujący celowo użył formularza HTML z auto-submitem, a nie fetch(). To nie przypadek:

fetch()/XMLHttpRequest do innej domeny (cross-origin) podlega CORS — przeglądarka wysyła żądanie, ale ofiara i atakujący nie mogą odczytać odpowiedzi bez zgody serwera bankowego (nagłówek Access-Control-Allow-Origin). Serwer bankowy jej nie da, więc fetch się nie nadaje do wysyłania takiego żądania w klasycznym scenariuszu CSRF (choć samo żądanie i tak leci — CORS blokuje tylko odczyt odpowiedzi, nie samo wysłanie).
Formularz HTML nie podlega CORS w ogóle — to fundamentalna różnica. Przeglądarki od zawsze pozwalają na cross-site form submission (to legalna, stara funkcja webu), więc atakujący nie musi znać ani odczytywać odpowiedzi banku — wystarczy że żądanie dotrze i zostanie wykonane.

Dlatego klasyczny CSRF prawie zawsze wykorzystuje <form>, a nie fetch() — to właśnie dlatego Twój oryginalny kod z pierwszej wiadomości był poprawnie skonstruowany.
