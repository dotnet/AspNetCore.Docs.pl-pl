```console
npm run release
```

To polecenie generuje zasoby po stronie klienta, które mają być obsługiwane podczas uruchamiania aplikacji. Zasoby są umieszczane w folderze *wwwroot.*

Pakiet Webpack wykonał następujące zadania:

* Przeczyszczona zawartość katalogu *wwwroot.*
* Przekonwertowano kod maszynowy na język JavaScript w procesie zwanym *transpilacją*.
* Zniekształcony wygenerowany JavaScript, aby zmniejszyć rozmiar pliku w procesie znanym jako *minyfikacja*.
* Skopiowano przetworzone pliki JavaScript, CSS i HTML z *src* do katalogu *wwwroot.*
* Wstrzykuje się następujące elementy do pliku *wwwroot/index.html:*
  * Tag, `<link>` odwołujący się do *wwwroot/main.\< hash\>.css.* Ten tag jest umieszczany `</head>` bezpośrednio przed tagiem zamykającym.
  * Tag, `<script>` odwołujący się do zmętnionego *wwwroot/main.\< hash\>.js.* Ten tag jest umieszczany `</body>` bezpośrednio przed tagiem zamykającym.