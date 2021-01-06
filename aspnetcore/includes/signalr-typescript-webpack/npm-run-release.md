```console
npm run release
```

To polecenie generuje zasoby po stronie klienta, które mają być obsługiwane podczas uruchamiania aplikacji. Elementy zawartości są umieszczane w folderze *wwwroot* .

Pakiet WebPack ukończył następujące zadania:

* Przeczyszczanie zawartości katalogu *wwwroot* .
* Przekonwertowane TypeScript na JavaScript w procesie znanym jako *transpilation*.
* Zniekształcona wygenerowany kod JavaScript, aby zmniejszyć rozmiar pliku w procesie znanym jako *minifikacja*.
* Skopiowano przetworzone pliki JavaScript, CSS i HTML z pliku *src* do katalogu *wwwroot* .
* Następujące elementy zostały dodane do pliku *wwwroot/index.html* :
  * `<link>`Tag, który odwołuje się do elementu *wwwroot/Main. \<hash\> . plik CSS* . Ten tag jest umieszczany bezpośrednio przed tagiem zamykającym `</head>` .
  * `<script>`Tag, który odwołuje się do zminimalizowanego *wwwroot/Main. \<hash\> . plik js* . Ten tag jest umieszczany bezpośrednio przed tagiem zamykającym `</body>` .