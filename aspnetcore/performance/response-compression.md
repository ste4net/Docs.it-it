---
title: Middleware di compressione di risposta per ASP.NET Core
author: guardrex
description: Informazioni sulla compressione di risposta e come utilizzare il Middleware di compressione risposta nelle applicazioni ASP.NET Core.
manager: wpickett
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/20/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: performance/response-compression
ms.openlocfilehash: e970e74547f1f3efaf719c1f9e26918f34788005
ms.sourcegitcommit: 726ffab258070b4fe6cf950bf030ce10c0c07bb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34734627"
---
# <a name="response-compression-middleware-for-aspnet-core"></a>Middleware di compressione di risposta per ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples) ([procedura per il download](xref:tutorials/index#how-to-download-a-sample))

Larghezza di banda di rete è una risorsa limitata. Riduzione delle dimensioni della risposta in genere aumenta la velocità di risposta di un'app, spesso in modo significativo. Un modo per ridurre le dimensioni di payload consiste nella compressione delle risposte di un'app.

## <a name="when-to-use-response-compression-middleware"></a>Quando utilizzare il Middleware di compressione risposta

Utilizzare le tecnologie di compressione risposta basata sul server in IIS, Apache o Nginx. Le prestazioni del middleware probabilmente non corrisponderanno a quello dei moduli server. [Server HTTP. sys](xref:fundamentals/servers/httpsys) e [Kestrel](xref:fundamentals/servers/kestrel) attualmente non offre il supporto della compressione incorporata.

Utilizzare il Middleware di compressione risposta quando si è:

* Impossibile utilizzare le tecnologie di compressione basata su server seguenti:
  * [Modulo di compressione dinamica di IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Modulo mod_deflate Apache](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx compressione e decompressione](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hosting direttamente in:
  * [Server HTTP. sys](xref:fundamentals/servers/httpsys) (in precedenza denominato [WebListener](xref:fundamentals/servers/weblistener))
  * [Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Compressione di risposta

In genere, le risposte compresse in modo non nativo possono trarre vantaggio dalla compressione di risposta. Le risposte compresse in modo non nativo in genere includono: CSS, JavaScript, HTML, XML e JSON. Non deve comprimere asset compresso in modo nativo, ad esempio i file PNG. Se si tenta di comprimere ulteriormente una risposta compressa in modo nativo, qualsiasi lieve riduzione aggiuntiva in fase di dimensioni e la trasmissione verrà probabilmente essere rimane in ombra base al tempo impiegato per elaborare la compressione. Non comprimere i file più piccoli di circa 150-1000 byte (a seconda del contenuto del file e l'efficienza della compressione). L'overhead di compressione di file di piccole dimensioni può produrre un file compresso più grande del file non compresso.

Quando un client può elaborare il contenuto compresso, il client deve informare il server delle rispettive funzionalità inviando il `Accept-Encoding` intestazione con la richiesta. Quando un server invia il contenuto compresso, deve includere informazioni di `Content-Encoding` intestazione nella modalità di codifica risposta compressa. Designazioni di codifica del contenuto supportati dal middleware vengono visualizzati nella tabella seguente.

| `Accept-Encoding` valori di intestazione | Middleware supportati | Descrizione                                                 |
| ------------------------------- | :------------------: | ----------------------------------------------------------- |
| `br`                            | No                   | Formato di dati compressi Brotli                               |
| `compress`                      | No                   | Formato di dati "comprimere" UNIX                                 |
| `deflate`                       | No                   | i dati compressi in formato "zlib" dati "deflate"     |
| `exi`                           | No                   | W3C XML efficiente interscambio                               |
| `gzip`                          | Sì (impostazione predefinita)        | formato del file gzip                                            |
| `identity`                      | Yes                  | "Nessuna codifica" identificatore: la risposta non deve essere codificata. |
| `pack200-gzip`                  | No                   | Formato di trasferimento di rete per gli archivi di Java                   |
| `*`                             | Yes                  | Qualsiasi contenuto disponibile, la codifica non è richiesto in modo esplicito     |

Per ulteriori informazioni, vedere il [IANA ufficiale elenco codifica contenuto](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Il middleware consente di aggiungere provider di compressione aggiuntiva per personalizzato `Accept-Encoding` valori di intestazione. Per ulteriori informazioni, vedere [provider personalizzati](#custom-providers) sotto.

Il middleware è in grado di rispondere al valore di qualità (qvalue, `q`) quando vengono inviati dal client per gli schemi di compressione la priorità di ponderazione. Per ulteriori informazioni, vedere [RFC 7231: codifica](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Gli algoritmi di compressione sono soggetti a un compromesso tra velocità di compressione e l'efficacia della compressione. *Efficacia* in questo contesto si riferisce alla dimensione dell'output dopo la compressione. La dimensione più piccola viene ottenuta da più *ottimale* compressione.

Le intestazioni coinvolti nella richiesta, invio, la memorizzazione nella cache e la ricezione di contenuto compresso sono descritti nella tabella riportata di seguito.

| Header             | Ruolo |
| ------------------ | ---- |
| `Accept-Encoding`  | Inviata dal client al server per indicare gli schemi accettabili per il client di codifica del contenuto. |
| `Content-Encoding` | Inviati dal server al client per indicare la codifica del contenuto nel payload. |
| `Content-Length`   | Quando si verifica la compressione, il `Content-Length` intestazione viene rimosso, poiché le modifiche di contenuto del corpo quando la risposta è compresso. |
| `Content-MD5`      | Quando si verifica la compressione, il `Content-MD5` intestazione viene rimosso, poiché è stato modificato il contenuto del corpo e l'hash non è più valido. |
| `Content-Type`     | Specifica il tipo MIME del contenuto. Ogni risposta è necessario specificare il relativo `Content-Type`. Il middleware controlla questo valore per determinare se la risposta deve essere compresso. Il middleware specifica un set di [predefinito di tipi MIME](#mime-types) che può codificare, ma è possibile sostituire o aggiungere i tipi MIME. |
| `Vary`             | Quando vengono inviati dal server con un valore di `Accept-Encoding` ai client e proxy, il `Vary` intestazione indica al client o proxy di memorizzazione nella cache (variare) le risposte in base al valore del `Accept-Encoding` intestazione della richiesta. Il risultato di restituzione del contenuto con il `Vary: Accept-Encoding` intestazione è che entrambi compressi e decompressi risposte memorizzate nella cache separatamente. |

È possibile esplorare le funzionalità del Middleware di compressione risposta con il [app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples). Nell'esempio viene illustrato:

* La compressione delle risposte di app con gzip e provider di compressione personalizzata.
* Come aggiungere un tipo MIME per l'elenco predefinito di tipi MIME per la compressione.

## <a name="package"></a>Pacchetto

::: moniker range="< aspnetcore-2.1"

Per includere il middleware nel progetto, aggiungere un riferimento per il [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacchetto. Questa funzionalità è disponibile per le app destinate ad ASP.NET Core 1.1 o versione successiva.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Per includere il middleware nel progetto, aggiungere un riferimento per il [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacchetto oppure utilizzare il [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).

::: moniker-end

## <a name="configuration"></a>Configurazione

Il codice seguente viene illustrato come abilitare il Middleware di compressione di risposta con la compressione gzip predefinita e per i tipi MIME predefiniti.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

> [!NOTE]
> Utilizzare uno strumento come [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), o [Postman](https://www.getpostman.com/) per impostare il `Accept-Encoding` intestazione della richiesta ed esaminare le intestazioni di risposta, dimensioni e corpo.

Inviare una richiesta per l'app di esempio senza il `Accept-Encoding` intestazione e osservare che la risposta non compressa. Il `Content-Encoding` e `Vary` intestazioni non sono presenti nella risposta.

![Finestra di Fiddler che mostra il risultato di una richiesta senza l'intestazione Accept-Encoding. La risposta non è compresso.](response-compression/_static/request-uncompressed.png)

Inviare una richiesta per l'app di esempio con il `Accept-Encoding: gzip` intestazione e osservare che la risposta è compresso. Il `Content-Encoding` e `Vary` le intestazioni sono presenti nella risposta.

![Finestra di Fiddler che mostra il risultato di una richiesta con l'intestazione Accept-Encoding e il valore gzip. Le intestazioni possono variare e Content-Encoding vengono aggiunti alla risposta. La risposta è compresso.](response-compression/_static/request-compressed.png)

## <a name="providers"></a>Provider

### <a name="gzipcompressionprovider"></a>GzipCompressionProvider

Usare la [GzipCompressionProvider](/dotnet/api/microsoft.aspnetcore.responsecompression.gzipcompressionprovider) per comprimere le risposte con gzip. Questo è il provider di compressione predefinito se non è specificato. È possibile impostare la compressione a livello con il [GzipCompressionProviderOptions](/dotnet/api/microsoft.aspnetcore.responsecompression.gzipcompressionprovideroptions).

Il provider di compressione gzip per impostazione predefinita il livello di compressione più veloce ([CompressionLevel.Fastest](/dotnet/api/system.io.compression.compressionlevel)), che potrebbe non produrre la compressione più efficiente. Se si desidera usare la compressione più efficiente, è possibile configurare il middleware per la compressione ottimale.

| Livello di compressione | Descrizione |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](/dotnet/api/system.io.compression.compressionlevel) | La compressione deve essere completata più rapidamente possibile, anche se l'output risultante non compressi in modo ottimale. |
| [CompressionLevel.NoCompression](/dotnet/api/system.io.compression.compressionlevel) | Non deve essere eseguita alcuna compressione. |
| [CompressionLevel.Optimal](/dotnet/api/system.io.compression.compressionlevel) | Le risposte devono essere compressi in modo ottimale, anche se la compressione richiede più tempo per completare. |

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5,12-15)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5,12-15)]

---

## <a name="mime-types"></a>MIME (tipi)

Il middleware specifica un set predefinito di tipi MIME per la compressione:

* `text/plain`
* `text/css`
* `application/javascript`
* `text/html`
* `application/xml`
* `text/xml`
* `application/json`
* `text/json`

È possibile sostituire o aggiungere tipi MIME con le opzioni del Middleware di compressione di risposta. Si noti che con caratteri jolly MIME tipi, ad esempio `text/*` non sono supportate. L'app di esempio aggiunge un tipo MIME per `image/svg+xml` e consente di comprimere e serve ASP.NET Core immagine del banner (*banner.svg*).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7-9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

---

### <a name="custom-providers"></a>Provider personalizzati

È possibile creare le implementazioni di compressione personalizzata con [ICompressionProvider](/dotnet/api/microsoft.aspnetcore.responsecompression.icompressionprovider). Il [EncodingName](/dotnet/api/microsoft.aspnetcore.responsecompression.icompressionprovider.encodingname) rappresenta il contenuto da questo codifica `ICompressionProvider` produce. Il middleware utilizza queste informazioni per scegliere il provider in base all'elenco specificato nella `Accept-Encoding` intestazione della richiesta.

Usando l'app di esempio, il client invia una richiesta con la `Accept-Encoding: mycustomcompression` intestazione. Il middleware utilizza l'implementazione della compressione personalizzata e restituisce la risposta con un `Content-Encoding: mycustomcompression` intestazione. Il client deve essere in grado di decomprimere la codifica personalizzata in ordine per un'implementazione personalizzata di compressione funzionare.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5,12-15)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5,12-15)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

---

Inviare una richiesta per l'app di esempio con il `Accept-Encoding: mycustomcompression` intestazione e osservare le intestazioni di risposta. Il `Vary` e `Content-Encoding` le intestazioni sono presenti nella risposta. L'esempio non è compressa il corpo della risposta (non illustrato). Non è un'implementazione della compressione nel `CustomCompressionProvider` classe dell'esempio. Tuttavia, nell'esempio viene illustrato dove si implementa un algoritmo di compressione.

![Finestra di Fiddler che mostra il risultato di una richiesta con l'intestazione Accept-Encoding e il valore mycustomcompression. Le intestazioni possono variare e Content-Encoding vengono aggiunti alla risposta.](response-compression/_static/request-custom-compression.png)

## <a name="compression-with-secure-protocol"></a>Compressione con protocollo sicuro

Le risposte compresse tramite connessioni protette possono essere controllate con i `EnableForHttps` opzione, è disabilitato per impostazione predefinita. Utilizzo di compressione con pagine generate in modo dinamico può causare problemi di sicurezza, ad esempio il [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [violazione](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacchi.

## <a name="adding-the-vary-header"></a>Aggiunta di intestazione Vary

::: moniker range=">= aspnetcore-2.0"

Quando la compressione delle risposte in base il `Accept-Encoding` intestazione, vi sono potenzialmente più versioni compresse della risposta e una versione non compressa. Per indicare di cache del client e del proxy che più versioni esistano e devono essere archiviate, il `Vary` intestazione viene aggiunto con un `Accept-Encoding` valore. In ASP.NET Core 2.0 o versione successiva, il middleware aggiunge il `Vary` intestazione automaticamente quando la risposta è compresso.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Quando la compressione delle risposte in base il `Accept-Encoding` intestazione, vi sono potenzialmente più versioni compresse della risposta e una versione non compressa. Per indicare di cache del client e del proxy che più versioni esistano e devono essere archiviate, il `Vary` intestazione viene aggiunto con un `Accept-Encoding` valore. In ASP.NET Core 1.x, aggiunta di `Vary` intestazione nella risposta viene eseguita manualmente:

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problema di middleware dietro un proxy inverso Nginx

Quando una richiesta viene inviata da Nginx, il `Accept-Encoding` intestazione viene rimosso. Ciò impedisce il middleware di compressione della risposta. Per ulteriori informazioni, vedere [NGINX: la compressione e decompressione](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Questo problema viene rilevato da [scoprire la compressione di tipo pass-through per Nginx (BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Utilizzo con la compressione dinamica di IIS

Se si dispone di un active modulo di compressione dinamica IIS configurata a livello di server che si desidera disabilitare per un'app, è possibile farlo con un componente aggiuntivo per il *Web. config* file. Per altre informazioni, vedere [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Disabilitazione di moduli IIS).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Utilizzare uno strumento come [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), o [Postman](https://www.getpostman.com/), che consente di impostare il `Accept-Encoding` intestazione della richiesta ed esaminare le intestazioni di risposta, dimensioni e corpo. Il Middleware di compressione risposta comprime le risposte che soddisfano le condizioni seguenti:

* Il `Accept-Encoding` è presente con un valore di intestazione `gzip`, `*`, o codifica personalizzata che corrisponde a un provider di compressione personalizzata che è stata stabilita. Il valore non deve essere `identity` o hanno un valore di qualità (qvalue, `q`) impostazione pari a 0 (zero).
* Il tipo MIME (`Content-Type`) deve essere impostata e deve corrispondere a un tipo MIME configurato nel [ResponseCompressionOptions](/dotnet/api/microsoft.aspnetcore.responsecompression.responsecompressionoptions).
* La richiesta non deve includere il `Content-Range` intestazione.
* La richiesta deve utilizzare il protocollo non sicuro (http), a meno che un protocollo sicuro (https) viene configurato nelle opzioni del Middleware di compressione di risposta. *Si noti il pericolo [descritto in precedenza](#compression-with-secure-protocol) durante l'abilitazione della compressione del contenuto protetta.*

## <a name="additional-resources"></a>Risorse aggiuntive

* [Avvio dell'applicazione](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
* [Mozilla Developer Network: Codifica](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Della sezione RFC 7231 3.1.2.1: Chiaramente tutti i codici del contenuto](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 sezione 4.2.3: Codifica Gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Versione specifica del formato file GZIP 4.3](http://www.ietf.org/rfc/rfc1952.txt)
