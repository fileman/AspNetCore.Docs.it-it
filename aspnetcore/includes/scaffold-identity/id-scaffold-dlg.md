Eseguire l'impalcatura delle identità:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal **Esplora soluzioni**, fare doppio clic sul progetto > **Add** > **nuovo elemento di scaffolding**.
* Dal riquadro sinistro della finestra di **Add Scaffold** finestra di dialogo, seleziona **identità** > **aggiungere**.
* Nella finestra di dialogo **Aggiungi identità** selezionare le opzioni desiderate.
  * Selezionare la pagina layout esistente. in alternativa, il file di layout verrà sovrascritto con un markup errato. Ad esempio `~/Pages/Shared/_Layout.cshtml` per Razor pages `~/Views/Shared/_Layout.cshtml` per i progetti MVC
  * Selezionare il **+** per creare un nuovo pulsante **classe contesto dati**.
* Selezionare **aggiungere**.

# <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Se l'utilità di scaffolding di ASP.NET Core non è stato precedentemente installato, installarlo ora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Aggiungere un riferimento al pacchetto [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al file di\*progetto (con estensione csproj). Eseguire il comando seguente nella directory del progetto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Eseguire il comando seguente per elencare le opzioni di utilità di scaffolding di identità:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Nella cartella del progetto eseguire l'impalcatura di identità con le opzioni desiderate. Ad esempio, per configurare Identity con l'interfaccia utente predefinita e il numero minimo di file, eseguire il comando seguente:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
