---
title: Eseguire la migrazione dell'autenticazione e dell'identità a ASP.NET Core
author: ardalis
description: Informazioni su come eseguire la migrazione dell'autenticazione e dell'identità da un progetto MVC ASP.NET a un progetto MVC ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022278"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Eseguire la migrazione dell'autenticazione e dell'identità a ASP.NET Core

Di [Steve Smith](https://ardalis.com/)

Nell'articolo precedente è stata [eseguita la migrazione della configurazione da un progetto mvc ASP.NET a ASP.NET Core MVC](xref:migration/configuration). Questo articolo illustra come eseguire la migrazione delle funzionalità di registrazione, accesso e gestione degli utenti.

## <a name="configure-identity-and-membership"></a>Configurare identità e appartenenza

In ASP.NET MVC le funzionalità di autenticazione e identità vengono configurate usando ASP.NET Identity in *Startup.auth.cs* e *IdentityConfig.cs*, che si trova nella cartella *app_start* . In ASP.NET Core MVC queste funzionalità sono configurate in *Startup.cs*.

Installare i `Microsoft.AspNetCore.Identity.EntityFrameworkCore` pacchetti `Microsoft.AspNetCore.Authentication.Cookies` NuGet e.

Quindi, aprire *Startup.cs* e aggiornare il `Startup.ConfigureServices` metodo per usare Entity Framework e i servizi Identity:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

A questo punto, esistono due tipi a cui si fa riferimento nel codice precedente, di cui non è ancora stata eseguita la migrazione dal `ApplicationDbContext` progetto `ApplicationUser`MVC ASP.NET: e. Creare una nuova cartella Models nel progetto ASP.NET Core e aggiungere due classi alla classe corrispondente a questi tipi. Le versioni di queste classi di ASP.NET MVC sono disponibili in */Models/IdentityModels.cs*, ma si userà un file per classe nel progetto migrato, perché questo è più chiaro.

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

Il progetto Web di avvio ASP.NET Core MVC non include molte personalizzazioni degli utenti o `ApplicationDbContext`. Quando si esegue la migrazione di un'app reale, è anche necessario eseguire la migrazione di tutti i metodi e le proprietà personalizzate `DbContext` dell'utente e delle classi dell'app, oltre alle altre classi di modelli usate dall'app. Se `DbContext` , ad esempio, è presente `DbSet<Album>`un oggetto, è necessario eseguire `Album` la migrazione della classe.

Con questi file, è possibile compilare il file *Startup.cs* aggiornando le relative `using` istruzioni:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

L'app è ora pronta per supportare i servizi di autenticazione e di identità. È sufficiente che queste funzionalità siano esposte agli utenti.

## <a name="migrate-registration-and-login-logic"></a>Eseguire la migrazione della logica di registrazione e accesso

Con i servizi di identità configurati per l'app e l'accesso ai dati configurati con Entity Framework e SQL Server, è possibile aggiungere il supporto per la registrazione e l'accesso all'app. Si ricordi che [in precedenza nel processo di migrazione](xref:migration/mvc#migrate-the-layout-file) è stato impostato come commento un riferimento a *_LoginPartial* in *file. cshtml*. A questo punto è possibile tornare a tale codice, rimuovere il commento e aggiungere i controller e le visualizzazioni necessari per supportare la funzionalità di accesso.

Rimuovere il commento `@Html.Partial` dalla riga in *file. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

A questo punto, aggiungere una nuova visualizzazione Razor denominata *_LoginPartial* alla cartella *Views/Shared* :

Aggiornare *_LoginPartial. cshtml* con il codice seguente (sostituire tutto il contenuto):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

A questo punto, dovrebbe essere possibile aggiornare il sito nel browser.

## <a name="summary"></a>Riepilogo

ASP.NET Core introduce le modifiche apportate alle funzionalità di ASP.NET Identity. In questo articolo è stato illustrato come eseguire la migrazione delle funzionalità di autenticazione e gestione utenti di ASP.NET Identity ASP.NET Core.
