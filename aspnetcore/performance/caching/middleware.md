---
title: Middleware di memorizzazione nella cache delle risposte in ASP.NET Core
author: guardrex
description: Informazioni su come configurare e usare il middleware di memorizzazione nella cache delle risposte in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/caching/middleware
ms.openlocfilehash: 838a08c12316d218501f26d5905f9e31ab93dfc9
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994234"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Middleware di memorizzazione nella cache delle risposte in ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [John Luo](https://github.com/JunTaoLuo)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

Questo articolo illustra come configurare il middleware di memorizzazione nella cache delle risposte in un'app ASP.NET Core. Il middleware determina quando le risposte sono memorizzabili nella cache, archivia le risposte e fornisce risposte dalla cache. Per un'introduzione alla memorizzazione nella cache HTTP e all'attributo [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) , vedere [caching delle risposte](xref:performance/caching/response).

## <a name="configuration"></a>Configurazione

::: moniker range=">= aspnetcore-3.0"

Il middleware di memorizzazione nella cache delle risposte è reso disponibile dal pacchetto [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) , che viene aggiunto in modo implicito alle app ASP.NET Core.

In `Startup.ConfigureServices`aggiungere il middleware di caching della risposta alla raccolta di servizi:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configurare l'app per l'uso del middleware con <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> il metodo di estensione, che aggiunge il middleware alla pipeline di elaborazione `Startup.Configure`delle richieste in:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

L'app di esempio aggiunge intestazioni per controllare la memorizzazione nella cache nelle richieste successive:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Memorizza nella cache le risposte memorizzabili nella cache per un massimo di 10 secondi.
* [Variazione](https://tools.ietf.org/html/rfc7231#section-7.1.4) Configura il middleware in modo da fornire una risposta memorizzata nella [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) cache solo se l'intestazione delle richieste successive corrisponde a quella della richiesta originale. &ndash;

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Il middleware di memorizzazione nella cache delle risposte memorizza nella cache solo le risposte del server che generano un codice di stato 200 (OK). Qualsiasi altra risposta, incluse le [pagine di errore](xref:fundamentals/error-handling), viene ignorata dal middleware.

> [!WARNING]
> Le risposte contenenti contenuto per i client autenticati devono essere contrassegnate come non memorizzabili nella cache per impedire al middleware di archiviare e servire tali risposte. Per informazioni dettagliate sul modo in cui il middleware determina se una risposta è memorizzabile nella cache, vedere [condizioni per la memorizzazione nella cache](#conditions-for-caching) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Usare il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

In `Startup.ConfigureServices`aggiungere il middleware di caching della risposta alla raccolta di servizi:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configurare l'app per l'uso del middleware con <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> il metodo di estensione, che aggiunge il middleware alla pipeline di elaborazione `Startup.Configure`delle richieste in:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

L'app di esempio aggiunge intestazioni per controllare la memorizzazione nella cache nelle richieste successive:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Memorizza nella cache le risposte memorizzabili nella cache per un massimo di 10 secondi.
* [Variazione](https://tools.ietf.org/html/rfc7231#section-7.1.4) Configura il middleware in modo da fornire una risposta memorizzata nella [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) cache solo se l'intestazione delle richieste successive corrisponde a quella della richiesta originale. &ndash;

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Il middleware di memorizzazione nella cache delle risposte memorizza nella cache solo le risposte del server che generano un codice di stato 200 (OK). Qualsiasi altra risposta, incluse le [pagine di errore](xref:fundamentals/error-handling), viene ignorata dal middleware.

> [!WARNING]
> Le risposte contenenti contenuto per i client autenticati devono essere contrassegnate come non memorizzabili nella cache per impedire al middleware di archiviare e servire tali risposte. Per informazioni dettagliate sul modo in cui il middleware determina se una risposta è memorizzabile nella cache, vedere [condizioni per la memorizzazione nella cache](#conditions-for-caching) .

::: moniker-end

## <a name="options"></a>Opzioni

Le opzioni di memorizzazione nella cache delle risposte sono illustrate nella tabella seguente.

| Opzione | Descrizione |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Dimensioni maggiori memorizzabili nella cache per il corpo della risposta in byte. Il valore predefinito è `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Limite delle dimensioni per il middleware della cache di risposta in byte. Il valore predefinito è `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determina se le risposte vengono memorizzate nella cache nei percorsi che fanno distinzione Il valore predefinito è `false`. |

Nell'esempio seguente il middleware viene configurato per:

* Risposte della cache con dimensioni del corpo inferiori o uguali a 1.024 byte.
* Archiviare le risposte in base ai percorsi con distinzione tra maiuscole e minuscole. Ad esempio, `/page1` e `/Page1` vengono archiviati separatamente.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Quando si usano i controller dell'API Web o MVC o i modelli di pagina Razor Pages, l'attributo [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) specifica i parametri necessari per impostare le intestazioni appropriate per la memorizzazione nella cache delle risposte. L'unico parametro dell' `[ResponseCache]` attributo che richiede rigorosamente il middleware è <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, che non corrisponde a un'intestazione HTTP effettiva. Per altre informazioni, vedere <xref:performance/caching/response#responsecache-attribute>.

Quando non si usa `[ResponseCache]` l'attributo, la memorizzazione nella cache delle risposte `VaryByQueryKeys`può essere variata con. Usare direttamente da [HttpContext. Features:](xref:Microsoft.AspNetCore.Http.HttpContext.Features) <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

L'utilizzo di un singolo valore `*` uguale `VaryByQueryKeys` a in varia la cache in base a tutti i parametri di query della richiesta.

## <a name="http-headers-used-by-response-caching-middleware"></a>Intestazioni HTTP usate dal middleware di memorizzazione nella cache delle risposte

Nella tabella seguente vengono fornite informazioni sulle intestazioni HTTP che influiscono sulla memorizzazione nella cache delle risposte.

| Intestazione | Dettagli |
| ------ | ------- |
| `Authorization` | Se l'intestazione esiste, la risposta non viene memorizzata nella cache. |
| `Cache-Control` | Il middleware considera solo le risposte di memorizzazione nella cache `public` contrassegnate con la direttiva della cache. Controllare la memorizzazione nella cache con i parametri seguenti:<ul><li>validità massima</li><li>max-stale&#8224;</li><li>min-Fresh</li><li>must-revalidate</li><li>No-cache</li><li>Nessun archivio</li><li>solo-if-Cached</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Se non viene specificato alcun limite `max-stale`a, il middleware non esegue alcuna azione.<br>&#8225;`proxy-revalidate`ha lo stesso effetto di `must-revalidate`.<br><br>Per ulteriori informazioni, vedere [RFC 7231: Richieste di direttive](https://tools.ietf.org/html/rfc7234#section-5.2.1)di controllo della cache. |
| `Pragma` | Un' `Pragma: no-cache` intestazione nella richiesta produce lo stesso effetto di `Cache-Control: no-cache`. Questa intestazione viene sottoposta a override dalle direttive pertinenti `Cache-Control` nell'intestazione, se presente. Considerato per compatibilità con le versioni precedenti di HTTP/1.0. |
| `Set-Cookie` | Se l'intestazione esiste, la risposta non viene memorizzata nella cache. Qualsiasi middleware nella pipeline di elaborazione delle richieste che imposta uno o più cookie impedisce al middleware di caching della risposta di memorizzare nella cache la risposta, ad esempio il [provider TempData basato su cookie](xref:fundamentals/app-state#tempdata).  |
| `Vary` | L' `Vary` intestazione viene utilizzata per variare la risposta memorizzata nella cache da un'altra intestazione. Ad esempio, memorizzare nella cache le risposte per codifica `Vary: Accept-Encoding` includendo l'intestazione, che memorizza nella cache le `Accept-Encoding: gzip` risposte `Accept-Encoding: text/plain` per le richieste con intestazioni e separatamente. Una risposta con un valore di `*` intestazione non viene mai archiviata. |
| `Expires` | Una risposta ritenuta obsoleta da questa intestazione non viene archiviata o recuperata a meno `Cache-Control` che non venga sottoposta a override da altre intestazioni. |
| `If-None-Match` | La risposta completa viene servita dalla cache se il valore non `*` è e `ETag` la della risposta non corrisponde ad alcuno dei valori forniti. In caso contrario, viene servita una risposta 304 (non modificata). |
| `If-Modified-Since` | Se l' `If-None-Match` intestazione non è presente, viene fornita una risposta completa dalla cache se la data di risposta memorizzata nella cache è successiva al valore specificato. In caso contrario, viene servita una risposta *304 non modificata* . |
| `Date` | Quando viene servito dalla cache, `Date` l'intestazione viene impostata dal middleware se non è stata specificata nella risposta originale. |
| `Content-Length` | Quando viene servito dalla cache, `Content-Length` l'intestazione viene impostata dal middleware se non è stata specificata nella risposta originale. |
| `Age` | L' `Age` intestazione inviata nella risposta originale viene ignorata. Il middleware calcola un nuovo valore quando serve una risposta memorizzata nella cache. |

## <a name="caching-respects-request-cache-control-directives"></a>Caching rispetta le direttive di controllo della cache delle richieste

Il middleware rispetta le regole della [specifica HTTP 1,1 Caching](https://tools.ietf.org/html/rfc7234#section-5.2). Le regole richiedono una cache per rispettare un'intestazione `Cache-Control` valida inviata dal client. In base alla specifica, un client può effettuare richieste con `no-cache` un valore di intestazione e forzare il server a generare una nuova risposta per ogni richiesta. Attualmente, non esiste alcun controllo dello sviluppatore su questo comportamento di Caching quando si usa il middleware perché il middleware rispetta la specifica di Caching ufficiale.

Per un maggiore controllo sul comportamento della memorizzazione nella cache, esplorare altre funzionalità di memorizzazione nella cache di ASP.NET Core. Vedere gli argomenti seguenti:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>risoluzione dei problemi

Se il comportamento di memorizzazione nella cache non è quello previsto, verificare che le risposte siano memorizzabili nella cache e in grado di essere servite dalla cache. Esaminare le intestazioni in ingresso della richiesta e le intestazioni in uscita della risposta. Abilitare la [registrazione](xref:fundamentals/logging/index) per facilitare il debug.

Durante il test e la risoluzione dei problemi relativi al comportamento di Caching, un browser può impostare intestazioni di richiesta che influiscono sulla memorizzazione nella cache in modi indesiderati Ad esempio, un browser può impostare l' `Cache-Control` intestazione su `no-cache` o `max-age=0` durante l'aggiornamento di una pagina. Gli strumenti seguenti possono impostare in modo esplicito le intestazioni delle richieste e sono preferiti per il test della memorizzazione nella cache:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Condizioni per la memorizzazione nella cache

* La richiesta deve avere come risultato una risposta del server con un codice di stato 200 (OK).
* Il metodo di richiesta deve essere GET o HEAD.
* In `Startup.Configure`il middleware di memorizzazione nella cache delle risposte deve essere inserito prima del middleware che richiede la memorizzazione nella cache. Per altre informazioni, vedere <xref:fundamentals/middleware/index>.
* L' `Authorization` intestazione non deve essere presente.
* `Cache-Control`i parametri di intestazione devono essere validi e la risposta deve essere `public` contrassegnata e `private`non contrassegnata.
* L' `Pragma: no-cache` intestazione non deve essere presente se l' `Cache-Control` intestazione non è presente, in `Cache-Control` quanto l'intestazione `Pragma` sostituisce l'intestazione quando è presente.
* L' `Set-Cookie` intestazione non deve essere presente.
* `Vary`i parametri di intestazione devono essere validi e non `*`uguali a.
* Il `Content-Length` valore dell'intestazione (se impostato) deve corrispondere alla dimensione del corpo della risposta.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> Non viene utilizzato.
* La risposta non deve essere obsoleta come specificato dall' `Expires` intestazione `max-age` e dalle direttive della cache e `s-maxage` .
* Il buffer delle risposte deve avere esito positivo. La dimensione della risposta deve essere minore di quella configurata o <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>predefinita. Le dimensioni del corpo della risposta devono essere inferiori a quelle configurate <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>o predefinite.
* La risposta deve essere memorizzata nella cache in base alle specifiche [RFC 7234](https://tools.ietf.org/html/rfc7234) . La `no-store` direttiva, ad esempio, non deve esistere nei campi di intestazione della richiesta o della risposta. Vedere *la sezione 3: Archiviazione* delle risposte nelle cache di [RFC 7234](https://tools.ietf.org/html/rfc7234) per i dettagli.

> [!NOTE]
> Il sistema antifalsificazione per la generazione di token protetti per evitare attacchi di richiesta intersito falsa (CSRF) imposta le `Cache-Control` intestazioni `Pragma` e su `no-cache` in modo che le risposte non vengano memorizzate nella cache. Per informazioni su come disabilitare i token antifalsificazione per gli elementi del form HTML, <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>vedere.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
