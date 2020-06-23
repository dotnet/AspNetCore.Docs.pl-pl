## <a name="share-interop-code-in-a-class-library"></a>Udostępnianie kodu międzyoperacyjnego w bibliotece klas

Kod międzyoperacyjny JS można uwzględnić w bibliotece klas, co umożliwia udostępnianie kodu w pakiecie NuGet.

Biblioteka klas obsługuje Osadzanie zasobów JavaScript w skompilowanym zestawie. Pliki JavaScript są umieszczane w `wwwroot` folderze. Narzędzia te zajmują się osadzaniem zasobów podczas kompilowania biblioteki.

Skompilowany pakiet NuGet jest przywoływany w pliku projektu aplikacji w taki sam sposób, w jaki jest przywoływany każdy pakiet NuGet. Po przywróceniu pakietu kod aplikacji może być wywoływany w języku JavaScript, tak jakby był w języku C#.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components/class-libraries>.
