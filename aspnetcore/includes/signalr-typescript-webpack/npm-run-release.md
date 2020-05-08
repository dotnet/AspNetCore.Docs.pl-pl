```console
npm run release
```

To polecenie generuje zasoby po stronie klienta, które mają być obsługiwane podczas uruchamiania aplikacji. Elementy zawartości są umieszczane w folderze *wwwroot* .

Pakiet WebPack ukończył następujące zadania:

* Przeczyszczanie zawartości katalogu *wwwroot* .
* Przekonwertowane TypeScript na JavaScript w procesie znanym jako *transpilation*.
* Zniekształcona wygenerowany kod JavaScript, aby zmniejszyć rozmiar pliku w procesie znanym jako *minifikacja*.
* Skopiowano przetworzone pliki JavaScript, CSS i HTML z pliku *src* do katalogu *wwwroot* .
* Do pliku *wwwroot/index.html* zostały dodane następujące elementy:
  * Tag `<link>` , który odwołuje się do elementu *wwwroot\< /Main. hash\>. css* , plik. Ten tag jest umieszczany bezpośrednio przed tagiem `</head>` zamykającym.
  * Tag `<script>` , który odwołuje się do zminimalizowanego *wwwroot/\< Main. plik\>hash. js* . Ten tag jest umieszczany bezpośrednio przed tagiem `</body>` zamykającym.