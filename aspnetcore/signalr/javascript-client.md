---
title: Client ASP.NET Core SignalR JavaScript
author: rachelappel
description: Panoramica del client di ASP.NET Core SignalR JavaScript.
manager: wpickett
monikerRange: '>= aspnetcore-2.1'
ms.author: rachelap
ms.custom: mvc
ms.date: 05/29/2018
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: signalr/javascript-client
ms.openlocfilehash: 6ff888d3337bb53d435744009f4cc24b327ebcda
ms.sourcegitcommit: 7e87671fea9a5f36ca516616fe3b40b537f428d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2018
ms.locfileid: "35341938"
---
# <a name="aspnet-core-signalr-javascript-client"></a>Client ASP.NET Core SignalR JavaScript

[Rachel Appel](http://twitter.com/rachelappel)

La libreria client di ASP.NET Core SignalR JavaScript consente agli sviluppatori di chiamare il codice dell'hub sul lato server.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([procedura per il download](xref:tutorials/index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Installare il pacchetto client SignalR

La libreria client SignalR JavaScript viene recapitata come un [npm](https://www.npmjs.com/) pacchetto. Se si usa Visual Studio, eseguire `npm install` dal **Package Manager Console** mentre si trovano nella cartella radice. Per il codice di Visual Studio, eseguire il comando dal **Terminal integrata**.

  ```console
   npm init -y
   npm install @aspnet/signalr
  ```

Npm installa il contenuto del pacchetto nel *node_modules\\ @aspnet\signalr\dist\browser*  cartella. Creare una nuova cartella denominata *signalr* sotto il *wwwroot\\lib* cartella. Copia il *signalr.js* del file per il *wwwroot\lib\signalr* cartella.

## <a name="use-the-signalr-javascript-client"></a>Usare il client SignalR JavaScript

Fare riferimento a client SignalR JavaScript il `<script>` elemento.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Connettersi a un hub

Il codice seguente crea e avvia una connessione. Nome dell'hub viene fatta distinzione tra maiuscole e minuscole.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-12,28)]

### <a name="cross-origin-connections"></a>Connessioni tra le origini

In genere, i browser caricare le connessioni appartenenti allo stesso dominio della pagina richiesta. Tuttavia, esistono casi in cui è necessaria una connessione a un altro dominio.

Per impedire la lettura dei dati sensibili da un altro sito, di un sito dannoso [connessioni cross-origin](xref:security/cors) sono disabilitati per impostazione predefinita. Per consentire una richiesta multiorigine, abilitarla nel `Startup` classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Chiamare i metodi dell'hub da client

I client JavaScript chiamare metodi pubblici negli hub da usando `connection.invoke`. Il `invoke` metodo accetta due argomenti:

* Il nome del metodo dell'hub. Nell'esempio seguente, è il nome dell'hub `SendMessage`.
* Eventuali argomenti definiti nel metodo dell'hub. Nell'esempio seguente, è il nome dell'argomento `message`.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

## <a name="call-client-methods-from-hub"></a>Chiamare i metodi di client da hub

Per ricevere messaggi dall'hub, definire un metodo tramite il `connection.on` metodo.

* Il nome del metodo client JavaScript. Nell'esempio seguente, è il nome del metodo `ReceiveMessage`.
* Argomenti che hub passa al metodo. Nell'esempio seguente, il valore dell'argomento è `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Il codice precedente in `connection.on` viene eseguito quando sul lato server viene chiamato dal codice utilizzando il `SendAsync` metodo.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR determina quale metodo di client per chiamare creando una corrispondenza tra il nome del metodo e gli argomenti definiti `SendAsync` e `connection.on`.

> [!NOTE]
> Come procedura consigliata, chiamare `connection.start` dopo `connection.on` in modo che i gestori registrati prima di tutti i messaggi vengono ricevuti.

## <a name="error-handling-and-logging"></a>La registrazione e la gestione degli errori

Catena di una `catch` alla fine del metodo di `connection.start` metodo per gestire gli errori lato client. Utilizzare `console.error` agli errori di output alla console del browser.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=28)]

Traccia log lato client installazione passando un logger e il tipo di evento per l'accesso quando viene stabilita la connessione. I messaggi vengono registrati con il livello di log specificato e versioni successive. Livelli di log disponibili sono i seguenti:

* `signalR.LogLevel.Error` : Messaggi di errore. Registri `Error` solo i messaggi.
* `signalR.LogLevel.Warning` : Messaggi di avviso relativi potenziali errori. Registri `Warning`, e `Error` i messaggi.
* `signalR.LogLevel.Information` : Messaggi di stato senza errori. Registri `Information`, `Warning`, e `Error` i messaggi.
* `signalR.LogLevel.Trace` : I messaggi di traccia. Registra tutti gli elementi, inclusi i dati trasportati tra hub e client.

Usare la `configureLogging` metodo su `HubConnectionBuilder` per configurare il livello di registrazione. I messaggi vengono registrati nella console del browser.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="related-resources"></a>Risorse correlate

* [Hub](xref:signalr/hubs)
* [Client .NET](xref:signalr/dotnet-client)
* [Pubblicare in Azure](xref:signalr/publish-to-azure-web-app)
* [Abilitare le richieste tra le origini (CORS) in ASP.NET Core](xref:security/cors)