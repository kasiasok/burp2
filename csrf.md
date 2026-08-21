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

<br>

1. Atakujący hostuje CSRF PoC (HTML) na SWOIM serwerze (exploit server)
2. Ofiara odwiedza stronę atakującego → pobiera ten HTML do SWOJEJ przeglądarki
3. Przeglądarka ofiary WYKONUJE ten HTML lokalnie (auto-submit formularza)
4. Przeglądarka ofiary ma w swoim lokalnym magazynie ciasteczek wpis: domena bank.pl → session=xyz (bo ofiara zalogowała się tam wcześniej).
5. Kod HTML z serwera atakującego zawiera formularz z action="https://bank.pl/przelew".
6. Kiedy JavaScript wywołuje form.submit(), przeglądarka przygotowuje żądanie HTTP do bank.pl.
7. Zanim wyśle to żądanie, przeglądarka automatycznie sprawdza: "czy mam jakieś ciasteczka zapisane dla domeny bank.pl?" — to jest wbudowana, twarda reguła przeglądarki, działająca dla każdego żądania do dowolnej domeny, niezależnie skąd request pochodzi.
Znajduje session=xyz, dokleja nagłówek Cookie: session=xyz do żądania.
8. Dopiero teraz żądanie faktycznie leci przez sieć — bezpośrednio do bank.pl, nigdy przez serwer atakującego.
9. Bank odpowiada bezpośrednio przeglądarce ofiary, a przelew leci do atakującego.

<br>

RReferer jako walidacja — potwierdzenie

Tak: serwer banku sprawdza Referer, żeby zweryfikować, że żądanie rzeczywiście przyszło z jego własnej domeny (bank.pl/przelew.html), a nie z obcej strony (exploit-server.net/quiz.html). Jeśli serwer widzi Referer wskazujący na exploit-server.net, powinien odrzucić żądanie — to jest cała logika tej obrony.


<br>

Podsumowanie poprawnej zależności:

Co robisz w PoC	Co to omija

<meta name="referrer" content="no-referrer">	Walidację Referer (jeśli serwer akceptuje puste)
Formularz html zamiast fetch/img (który i tak nie ma znaczenia dla CSRF)
GET zamiast POST + auto-submit JS	Nic nie omija samo z siebie — kluczowe jest czy request jest subresource czy top-level navigation, co decyduje o SameSite=Lax


<br>

a dlaczego nie mozna podmienic referefa w poc na wlasciwy zamiast pusty?

Bo Referer to nagłówek generowany i kontrolowany wyłącznie przez przeglądarkę, nie przez JavaScript ani przez atakującego. Nie masz do niego zapisu z poziomu strony WWW — to fundamentalne ograniczenie API przeglądarek, nie przypadkowa luka.
