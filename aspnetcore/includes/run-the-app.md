# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację. Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Localhost obsługuje tylko żądania sieci web z komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Naciśnij **klawisze Ctrl-F5,** aby uruchomić bez debugera.

  Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `http://localhost:5001`przeglądarkę i przechodzi do . Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Localhost obsługuje tylko żądania sieci web z komputera lokalnego.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* W programie Visual Studio naciśnij **klawisz Opt-Cmd-Return,** aby uruchomić go bez debugera. Możesz też przejść do paska menu i przejść do **pozycji Uruchom>Start bez debugowania**.

  Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `http://localhost:5001`przeglądarkę i przechodzi do .

<!-- End of VS tabs -->

---