---
title: JSON Patch nell'API Web ASP.NET Core
author: tdykstra
description: Informazioni su come gestire le richieste JSON Patch in un'API Web ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 14710e6431a2a7ce60fa7f190bef184da85281a0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888416"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JSON Patch nell'API Web ASP.NET Core

[Tom Dykstra](https://github.com/tdykstra)

Questo articolo descrive come gestire le richieste JSON Patch in un'API Web ASP.NET Core.

## <a name="patch-http-request-method"></a>Metodo di richiesta HTTP PATCH

I metodi PUT e [PATCH](https://tools.ietf.org/html/rfc5789) vengono usati per aggiornare una risorsa esistente. La differenza tra i due metodi è che PUT sostituisce l'intera risorsa, mentre PATCH specifica solo le modifiche.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) è un formato per specificare gli aggiornamenti da applicare a una risorsa. Un documento JSON Patch è una matrice di *operazioni*. Ogni operazione identifica un determinato tipo di modifica, ad esempio l'aggiunta di un elemento della matrice o la sostituzione di un valore di proprietà.

Ad esempio, i documenti JSON seguenti rappresentano una risorsa, un documento di patch JSON per la risorsa e il risultato dell'applicazione delle operazioni patch.

### <a name="resource-example"></a>Esempio di risorsa

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Esempio di patch JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Nel codice JSON precedente:

* La proprietà `op` indica il tipo di operazione.
* La proprietà `path` indica l'elemento da aggiornare.
* La proprietà `value` fornisce il nuovo valore.

### <a name="resource-after-patch"></a>Risorsa dopo la patch

Ecco la risorsa dopo aver applicato il documento JSON Patch precedente:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Le modifiche apportate tramite l'applicazione di un documento JSON Patch a una risorsa sono atomiche: se qualsiasi operazione nell'elenco non riesce, non viene applicata alcuna operazione contenuta nell'elenco.

## <a name="path-syntax"></a>Sintassi di path

La proprietà [path](http://tools.ietf.org/html/rfc6901) di un oggetto operazione include barre tra livelli. Ad esempio `"/address/zipCode"`.

Vengono usati indici in base zero per specificare gli elementi della matrice. Il primo elemento della matrice `addresses` è in corrispondenza di `/addresses/0`. Per aggiungere (`add`) un elemento alla fine della matrice, usare un trattino (-) invece di un numero di indice: `/addresses/-`.

### <a name="operations"></a>Operazioni

La tabella seguente mostra le operazioni supportate in base a quanto definito nella [specifica JSON Patch](https://tools.ietf.org/html/rfc6902):

|Operazione  | Note |
|-----------|--------------------------------|
| `add`     | Aggiunge una proprietà o un elemento della matrice. Per la proprietà esistente: impostare il valore.|
| `remove`  | Rimuove una proprietà o un elemento della matrice. |
| `replace` | Uguale a `remove` seguita da `add` nella stessa posizione. |
| `move`    | Uguale a `remove` dall'origine seguita da `add` alla destinazione usando il valore dell'origine. |
| `copy`    | Uguale a `add` alla destinazione usando il valore dell'origine. |
| `test`    | Restituisce il codice di stato di richiesta riuscita se il valore in `path` = `value` fornito.|

## <a name="jsonpatch-in-aspnet-core"></a>JSON Patch in ASP.NET Core

L'implementazione ASP.NET Core di JSON Patch viene fornita nel pacchetto NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/). Il pacchetto è incluso nel metapacchetto [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

## <a name="action-method-code"></a>Codice del metodo di azione

In un controller API un metodo di azione per JSON Patch:

* Viene annotato con l'attributo `HttpPatch`.
* Accetta un oggetto `JsonPatchDocument<T>`, in genere con [FromBody].
* Chiama `ApplyTo` nel documento di patch per applicare le modifiche.

Di seguito è riportato un esempio:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Questo codice dell'app di esempio funziona con il modello `Customer` seguente.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Il metodo di azione di esempio:

* Costruisce un oggetto `Customer`.
* Applica la patch.
* Restituisce il risultato nel corpo della risposta.

 In un'app reale il codice recupererebbe i dati da un archivio, ad esempio un database, e aggiornerebbe il database dopo aver applicato la patch.

### <a name="model-state"></a>Stato del modello

L'esempio di metodo di azione precedente chiama un overload di `ApplyTo` che accetta lo stato del modello come uno dei propri parametri. Con questa opzione, è possibile ottenere messaggi di errore nelle risposte. L'esempio seguente mostra il corpo di una risposta 400 Richiesta non valida per un'operazione `test`:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Oggetti dinamici

L'esempio di metodo di azione seguente mostra come applicare una patch a un oggetto dinamico.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operazione add

* Se `path` punta a un elemento della matrice: inserisce un nuovo elemento prima di quello specificato da `path`.
* Se `path` punta a una proprietà: imposta il valore della proprietà.
* Se `path` punta a una posizione che non esiste:
  * Se la risorsa cui applicare la patch è un oggetto dinamico: aggiunge una proprietà.
  * Se la risorsa cui applicare la patch è un oggetto statico: la richiesta non riesce.

Il documento di patch di esempio seguente imposta il valore di `CustomerName` e aggiunge un oggetto `Order` alla fine della matrice `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operazione remove

* Se `path` punta a un elemento della matrice: rimuove l'elemento.
* Se `path` punta a una proprietà:
  * Se la risorsa cui applicare la patch è un oggetto dinamico: rimuove la proprietà.
  * Se la risorsa cui applicare la patch è un oggetto statico: 
    * Se la proprietà ammette valori Null: la imposta su Null.
    * Se la proprietà non ammette valori Null: la imposta su `default<T>`.

Il documento di patch di esempio seguente imposta `CustomerName` su Null ed elimina `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operazione replace

Questa operazione equivale a livello funzionale all'operazione `remove` seguita da un'operazione `add`.

Il documento di patch di esempio seguente imposta il valore di `CustomerName` e sostituisce `Orders[0]` con un nuovo oggetto `Order`.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operazione move

* Se `path` punta a un elemento della matrice: copia l'elemento `from` nella posizione dell'elemento `path`, quindi esegue un'operazione `remove` sull'elemento `from`.
* Se `path` punta a una proprietà: copia il valore della proprietà `from` nella proprietà `path`, quindi esegue un'operazione `remove` sulla proprietà `from`.
* Se `path` punta a una proprietà che non esiste:
  * Se la risorsa cui applicare la patch è un oggetto statico: la richiesta non riesce.
  * Se la risorsa cui applicare la patch è un oggetto dinamico: copia la proprietà `from` nella posizione indicata da `path`, quindi esegue un'operazione `remove` sulla proprietà `from`.

Il documento di patch di esempio seguente:

* Copia il valore di `Orders[0].OrderName` in `CustomerName`.
* Imposta `Orders[0].OrderName` su Null.
* Sposta `Orders[1]` prima di `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operazione copy

Questa operazione equivale a livello funzionale all'operazione `move` senza il passaggio `remove` finale.

Il documento di patch di esempio seguente:

* Copia il valore di `Orders[0].OrderName` in `CustomerName`.
* Inserisce una copia di `Orders[1]` prima di `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operazione test

Se il valore nella posizione indicata da `path` è diverso dal valore fornito in `value`, la richiesta non riesce. In questo caso, l'intera richiesta PATCH non riesce anche se tutte le altre operazioni nel documento di patch potrebbero riuscire.

L'operazione `test` viene usata in genere per impedire un aggiornamento in caso di conflitto di concorrenza.

Il documento di patch di esempio seguente non ha alcun effetto se il valore iniziale di `CustomerName` è "John", perché il test non riesce:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Ottenere il codice

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Come scaricare un esempio](xref:index#how-to-download-a-sample)).

Per testare l'esempio, eseguire l'app e inviare richieste HTTP con le impostazioni seguenti:

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metodo HTTP: `PATCH`
* Intestazione: `Content-Type: application/json-patch+json`
* Corpo: copiare e incollare uno degli esempi di documento di patch JSON dalla cartella del progetto *JSON*.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Specifica del metodo PATCH IETF RFC 5789](https://tools.ietf.org/html/rfc5789)
* [Specifica JSON Patch IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [Specifica del formato di percorso JSON Patch IETF RFC 6901](http://tools.ietf.org/html/rfc6901)
* [Documentazione di JSON Patch](http://jsonpatch.com/). Include collegamenti a risorse per la creazione di documenti JSON Patch.
* [Codice sorgente JSON Patch in ASP.NET Core](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)