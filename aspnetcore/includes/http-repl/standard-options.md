* `-F|--no-formatting`

  Flaga, której obecność pomija formatowanie odpowiedzi HTTP.

* `-h|--header`

  Ustawia nagłówek żądania HTTP. Obsługiwane są następujące dwa formaty wartości:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Określa plik, do którego powinna zostać zapisywana treść odpowiedzi HTTP. Na przykład `--response:body "C:\response.json"`. Plik zostanie utworzony, jeśli nie istnieje.

* `--response:headers`

  Określa plik, do którego mają być zapisywane nagłówki odpowiedzi HTTP. Na przykład `--response:headers "C:\response.txt"`. Plik zostanie utworzony, jeśli nie istnieje.

* `-s|--streaming`

  Flaga, której obecność umożliwia przesyłanie strumieniowe odpowiedzi HTTP.
