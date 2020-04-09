## <a name="share-interop-code-in-a-class-library"></a>Udostępnianie kodu interop w bibliotece klas

JS kod interop mogą być zawarte w bibliotece klas, co pozwala na udostępnianie kodu w pakiecie NuGet.

Biblioteka klas obsługuje osadzanie zasobów JavaScript w zestawie zbudowanym. Pliki JavaScript są umieszczane w folderze *wwwroot.* Oprzyrządowanie zajmuje się osadzanie zasobów podczas tworzenia biblioteki.

Utworzony pakiet NuGet odwołuje się w pliku projektu aplikacji w taki sam sposób, jak każdy pakiet NuGet odwołuje. Po przywróceniu pakietu kod aplikacji można wywołać w języku JavaScript, tak jakby był C#.

Aby uzyskać więcej informacji, zobacz <xref:blazor/class-libraries>.
