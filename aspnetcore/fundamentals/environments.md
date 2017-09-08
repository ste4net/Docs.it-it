---
title: "Utilizzo di più ambienti"
author: ardalis
description: 
keywords: ASP.NET di base, le impostazioni di ambiente, ASPNETCORE_ENVIRONMENT
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: 5e41920c9745f903d33fa25922727e920c1efc26
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2017
---
# <a name="working-with-multiple-environments"></a><span data-ttu-id="5d7a1-103">Utilizzo di più ambienti</span><span class="sxs-lookup"><span data-stu-id="5d7a1-103">Working with multiple environments</span></span>

<span data-ttu-id="5d7a1-104">Da [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="5d7a1-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="5d7a1-105">ASP.NET Core fornisce supporto per il controllo del comportamento dell'app in ambienti diversi, ad esempio sviluppo, gestione temporanea e produzione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-105">ASP.NET Core provides support for controlling app behavior across multiple environments, such as development, staging, and production.</span></span> <span data-ttu-id="5d7a1-106">Le variabili di ambiente vengono utilizzate per indicare l'ambiente di runtime, consentendo all'app di essere configurato per tale ambiente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-106">Environment variables are used to indicate the runtime environment, allowing the app to be configured for that environment.</span></span>

[<span data-ttu-id="5d7a1-107">Visualizzare o scaricare codice di esempio</span><span class="sxs-lookup"><span data-stu-id="5d7a1-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)

## <a name="development-staging-production"></a><span data-ttu-id="5d7a1-108">Sviluppo, gestione temporanea, produzione</span><span class="sxs-lookup"><span data-stu-id="5d7a1-108">Development, Staging, Production</span></span>

<span data-ttu-id="5d7a1-109">ASP.NET Core fa riferimento a un particolare [variabile di ambiente](https://github.com/aspnet/Home/wiki/Environment-Variables), `ASPNETCORE_ENVIRONMENT` per descrivere l'ambiente in cui è in esecuzione l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-109">ASP.NET Core references a particular [environment variable](https://github.com/aspnet/Home/wiki/Environment-Variables), `ASPNETCORE_ENVIRONMENT` to describe the environment the application is currently running in.</span></span> <span data-ttu-id="5d7a1-110">Questa variabile può essere impostata su qualsiasi valore desiderato, ma vengono utilizzati tre valori per convenzione: `Development`, `Staging`, e `Production`.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-110">This variable can be set to any value you like, but three values are used by convention: `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5d7a1-111">Si noterà questi valori utilizzati negli esempi e i modelli forniti con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-111">You will find these values used in the samples and templates provided with ASP.NET Core.</span></span>

<span data-ttu-id="5d7a1-112">Impostazione dell'ambiente corrente può essere rilevata a livello di codice all'interno dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-112">The current environment setting can be detected programmatically from within your application.</span></span> <span data-ttu-id="5d7a1-113">Inoltre, è possibile utilizzare l'ambiente [helper di tag](../mvc/views/tag-helpers/index.md) includere determinate sezioni del [vista](../mvc/views/index.md) in base all'ambiente dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-113">In addition, you can use the Environment [tag helper](../mvc/views/tag-helpers/index.md) to include certain sections in your [view](../mvc/views/index.md) based on the current application environment.</span></span>

<span data-ttu-id="5d7a1-114">Nota: In Windows e Mac OS, il nome dell'ambiente specificato viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-114">Note: On Windows and macOS, the specified environment name is case insensitive.</span></span> <span data-ttu-id="5d7a1-115">Se si imposta la variabile `Development` o `development` o `DEVELOPMENT` i risultati saranno uguali.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-115">Whether you set the variable to `Development` or `development` or `DEVELOPMENT` the results will be the same.</span></span> <span data-ttu-id="5d7a1-116">Tuttavia, Linux è un **tra maiuscole e minuscole** sistema operativo per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-116">However, Linux is a **case sensitive** OS by default.</span></span> <span data-ttu-id="5d7a1-117">Le variabili di ambiente, i nomi di file e le impostazioni richiedono distinzione maiuscole/minuscole.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-117">Environment variables, file names and settings require case sensitivity.</span></span>

### <a name="development"></a><span data-ttu-id="5d7a1-118">Sviluppo</span><span class="sxs-lookup"><span data-stu-id="5d7a1-118">Development</span></span>

<span data-ttu-id="5d7a1-119">Deve trattarsi di ambiente utilizzata quando si sviluppa un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-119">This should be the environment used when developing an application.</span></span> <span data-ttu-id="5d7a1-120">In genere viene utilizzato per abilitare le funzionalità che non si desidera rendere disponibili quando si esegue l'app nell'ambiente di produzione, ad esempio il [pagina eccezione developer](xref:fundamentals/error-handling#the-developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-120">It is typically used to enable features that you wouldn't want to be available when the app runs in production, such as the [developer exception page](xref:fundamentals/error-handling#the-developer-exception-page).</span></span>

<span data-ttu-id="5d7a1-121">Se si utilizza Visual Studio, è possibile configurare l'ambiente nei profili di debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-121">If you're using Visual Studio, the environment can be configured in your project's debug profiles.</span></span> <span data-ttu-id="5d7a1-122">Eseguire il debug di profili di specificare il [server](xref:fundamentals/servers/index) da utilizzare quando si avvia l'applicazione e delle variabili di ambiente da impostare.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-122">Debug profiles specify the [server](xref:fundamentals/servers/index) to use when launching the application and any environment variables to be set.</span></span> <span data-ttu-id="5d7a1-123">Il progetto può avere più profili di debug che impostare variabili di ambiente in modo diverso.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-123">Your project can have multiple debug profiles that set environment variables differently.</span></span> <span data-ttu-id="5d7a1-124">Gestire i profili usando il **Debug** scheda della finestra del progetto di applicazione web **proprietà** menu.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-124">You manage these profiles by using the **Debug** tab of your web application project's **Properties** menu.</span></span> <span data-ttu-id="5d7a1-125">I valori impostati nelle proprietà del progetto sono persistenti nel *launchSettings.json* file ed è inoltre possibile configurare profili modificando direttamente il file.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-125">The values you set in project properties are persisted in the *launchSettings.json* file, and you can also configure profiles by editing that file directly.</span></span>

<span data-ttu-id="5d7a1-126">Il profilo per IIS Express è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5d7a1-126">The profile for IIS Express is shown here:</span></span>

![Variabili di ambiente di impostazione delle proprietà del progetto](environments/_static/project-properties-debug.png)

<span data-ttu-id="5d7a1-128">Ecco un `launchSettings.json` file che include i profili per `Development` e `Staging`:</span><span class="sxs-lookup"><span data-stu-id="5d7a1-128">Here is a `launchSettings.json` file that includes profiles for `Development` and `Staging`:</span></span>

<span data-ttu-id="5d7a1-129">[!code-json[Principale](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]</span><span class="sxs-lookup"><span data-stu-id="5d7a1-129">[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]</span></span>

<span data-ttu-id="5d7a1-130">Le modifiche apportate ai profili di progetto non abbiano effetto, è necessario riavviare il server web utilizzato (in particolare, Kestrel devono essere riavviati prima che rileva le modifiche apportate al relativo ambiente).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-130">Changes made to project profiles may not take effect until the web server used is restarted (in particular, Kestrel must be restarted before it will detect changes made to its environment).</span></span>

>[!WARNING]
> <span data-ttu-id="5d7a1-131">Le variabili di ambiente archiviato in *launchSettings.json* non sono protetti in alcun modo e farà parte del repository di codice sorgente per il progetto, se si usa uno.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-131">Environment variables stored in *launchSettings.json* are not secured in any way and will be part of the source code repository for your project, if you use one.</span></span> <span data-ttu-id="5d7a1-132">**Non memorizzare mai le credenziali o altri dati riservati in questo file.**</span><span class="sxs-lookup"><span data-stu-id="5d7a1-132">**Never store credentials or other secret data in this file.**</span></span> <span data-ttu-id="5d7a1-133">Se occorre una posizione in cui archiviare tali dati, utilizzare il *Manager segreto* strumento descritto in [archiviazione sicura di segreti dell'app durante lo sviluppo](../security/app-secrets.md#security-app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-133">If you need a place to store such data, use the *Secret Manager* tool described in [Safe storage of app secrets during development](../security/app-secrets.md#security-app-secrets).</span></span>

### <a name="staging"></a><span data-ttu-id="5d7a1-134">Gestione temporanea</span><span class="sxs-lookup"><span data-stu-id="5d7a1-134">Staging</span></span>

<span data-ttu-id="5d7a1-135">Per convenzione, un `Staging` ambiente è un ambiente di pre-produzione usato per il test finale prima della distribuzione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-135">By convention, a `Staging` environment is a pre-production environment used for final testing before deployment to production.</span></span> <span data-ttu-id="5d7a1-136">In teoria, le sue caratteristiche fisiche devono rispecchiare quello di produzione, in modo che si verificano prima eventuali problemi che potrebbero verificarsi nell'ambiente di produzione nell'ambiente di gestione temporanea, in cui possono essere risolti senza impatto sugli utenti.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-136">Ideally, its physical characteristics should mirror that of production, so that any issues that may arise in production occur first in the staging environment, where they can be addressed without impact to users.</span></span>

### <a name="production"></a><span data-ttu-id="5d7a1-137">Produzione</span><span class="sxs-lookup"><span data-stu-id="5d7a1-137">Production</span></span>

<span data-ttu-id="5d7a1-138">Il `Production` ambiente è l'ambiente in cui l'applicazione viene eseguita quando è in tempo reale e utilizzati dagli utenti finali.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-138">The `Production` environment is the environment in which the application runs when it is live and being used by end users.</span></span> <span data-ttu-id="5d7a1-139">Questo ambiente deve essere configurato per ottimizzare la sicurezza, prestazioni e affidabilità dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-139">This environment should be configured to maximize security, performance, and application robustness.</span></span> <span data-ttu-id="5d7a1-140">Alcune impostazioni comuni che potrebbe avere un ambiente di produzione che verrebbero differisce dallo sviluppo includono:</span><span class="sxs-lookup"><span data-stu-id="5d7a1-140">Some common settings that a production environment might have that would differ from development include:</span></span>

* <span data-ttu-id="5d7a1-141">Attivare la memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="5d7a1-141">Turn on caching</span></span>

* <span data-ttu-id="5d7a1-142">Assicurarsi che tutte le risorse sul lato client sono inclusi, minimizzare e potenzialmente servite da una rete CDN</span><span class="sxs-lookup"><span data-stu-id="5d7a1-142">Ensure all client-side resources are bundled, minified, and potentially served from a CDN</span></span>

* <span data-ttu-id="5d7a1-143">Disattivare la diagnostica ErrorPages</span><span class="sxs-lookup"><span data-stu-id="5d7a1-143">Turn off diagnostic ErrorPages</span></span>

* <span data-ttu-id="5d7a1-144">Attivare le pagine di errore descrittivo</span><span class="sxs-lookup"><span data-stu-id="5d7a1-144">Turn on friendly error pages</span></span>

* <span data-ttu-id="5d7a1-145">Abilitare la registrazione e monitoraggio di produzione (ad esempio, [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))</span><span class="sxs-lookup"><span data-stu-id="5d7a1-145">Enable production logging and monitoring (for example, [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))</span></span>

<span data-ttu-id="5d7a1-146">Questo non deve essere un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-146">This is by no means meant to be a complete list.</span></span> <span data-ttu-id="5d7a1-147">È consigliabile evitare la dispersione controlli dell'ambiente in molte parti dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-147">It's best to avoid scattering environment checks in many parts of your application.</span></span> <span data-ttu-id="5d7a1-148">Al contrario, l'approccio consigliato è eseguire tali controlli all'interno dell'applicazione `Startup` classi ove possibile</span><span class="sxs-lookup"><span data-stu-id="5d7a1-148">Instead, the recommended approach is to perform such checks within the application's `Startup` class(es) wherever possible</span></span>

## <a name="setting-the-environment"></a><span data-ttu-id="5d7a1-149">Impostazione dell'ambiente</span><span class="sxs-lookup"><span data-stu-id="5d7a1-149">Setting the environment</span></span>

<span data-ttu-id="5d7a1-150">Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-150">The method for setting the environment depends on the operating system.</span></span>

### <a name="windows"></a><span data-ttu-id="5d7a1-151">Windows</span><span class="sxs-lookup"><span data-stu-id="5d7a1-151">Windows</span></span>
<span data-ttu-id="5d7a1-152">Per impostare il `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite `dotnet run`, vengono utilizzati i seguenti comandi</span><span class="sxs-lookup"><span data-stu-id="5d7a1-152">To set the `ASPNETCORE_ENVIRONMENT` for the current session, if the app is started using `dotnet run`, the following commands are used</span></span>

<span data-ttu-id="5d7a1-153">**Riga di comando**</span><span class="sxs-lookup"><span data-stu-id="5d7a1-153">**Command line**</span></span>
```
set ASPNETCORE_ENVIRONMENT=Development
```
<span data-ttu-id="5d7a1-154">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="5d7a1-154">**PowerShell**</span></span>
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="5d7a1-155">Questi comandi diventano effettive solo per la finestra corrente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-155">These commands take effect only for the current window.</span></span> <span data-ttu-id="5d7a1-156">Quando la finestra viene chiusa, l'impostazione ASPNETCORE_ENVIRONMENT verrà ripristinata l'impostazione predefinita o un valore di computer.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-156">When the window is closed, the ASPNETCORE_ENVIRONMENT setting reverts to the default setting or machine value.</span></span> <span data-ttu-id="5d7a1-157">Per impostare il valore globale all'apertura di Windows il **Pannello di controllo** > **sistema** > **impostazioni di sistema avanzate** e aggiungere o modificare il `ASPNETCORE_ENVIRONMENT` valore.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-157">In order to set the value globally on Windows open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value.</span></span>

![Sistema di proprietà avanzate](environments/_static/systemsetting_environment.png)

![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png) 

<span data-ttu-id="5d7a1-160">**Web. config**</span><span class="sxs-lookup"><span data-stu-id="5d7a1-160">**web.config**</span></span>

<span data-ttu-id="5d7a1-161">Vedere il *impostare variabili di ambiente* sezione la [riferimento di configurazione di ASP.NET Core modulo](xref:hosting/aspnet-core-module#setting-environment-variables) argomento.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-161">See the *Setting environment variables* section of the [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module#setting-environment-variables) topic.</span></span>

<span data-ttu-id="5d7a1-162">**Per ogni Pool di applicazioni IIS**</span><span class="sxs-lookup"><span data-stu-id="5d7a1-162">**Per IIS Application Pool**</span></span>

<span data-ttu-id="5d7a1-163">Se è necessario impostare le variabili di ambiente per le singole applicazioni in esecuzione nel pool di applicazioni isolate (supportata in IIS 10.0 +), vedere il *comando AppCmd.exe* sezione la [le variabili di ambiente \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) documentazione di riferimento di argomento in di IIS.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-163">If you need to set environment variables for individual apps running in isolated Application Pools (supported on IIS 10.0+), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

### <a name="macos"></a><span data-ttu-id="5d7a1-164">MacOS</span><span class="sxs-lookup"><span data-stu-id="5d7a1-164">macOS</span></span>
<span data-ttu-id="5d7a1-165">L'impostazione l'ambiente corrente per macOS può essere effettuata in linea, durante l'esecuzione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-165">Setting the current environment for macOS can be done in-line when running the application;</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
<span data-ttu-id="5d7a1-166">o tramite `export` impostarlo prima di eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-166">or using `export` to set it prior to running the app.</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
``` 
<span data-ttu-id="5d7a1-167">Le variabili di ambiente di livello del computer sono impostate *.bashrc* o *.bash_profile* file.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-167">Machine level environment variables are set in the *.bashrc*  or *.bash_profile* file.</span></span> <span data-ttu-id="5d7a1-168">Modificare il file utilizzando un editor di testo e aggiungere l'istruzione seguente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-168">Edit the file using any text editor and add the following statment.</span></span>

```
export ASPNETCORE_ENVIRONMENT=Development
```  

### <a name="linux"></a><span data-ttu-id="5d7a1-169">Linux</span><span class="sxs-lookup"><span data-stu-id="5d7a1-169">Linux</span></span>
<span data-ttu-id="5d7a1-170">Per le distribuzioni di Linux, usare il `export` comando dalla riga di comando per sessione in base a impostazioni delle variabili e *bash_profile* file per le impostazioni di ambiente di livello computer.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-170">For Linux distros, use the `export` command at the command line for session based variable settings and *bash_profile* file for machine level environment settings.</span></span>

## <a name="determining-the-environment-at-runtime"></a><span data-ttu-id="5d7a1-171">Determinare l'ambiente in fase di esecuzione</span><span class="sxs-lookup"><span data-stu-id="5d7a1-171">Determining the environment at runtime</span></span>

<span data-ttu-id="5d7a1-172">Il `IHostingEnvironment` servizio fornisce l'astrazione fondamentale per l'utilizzo di ambienti.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-172">The `IHostingEnvironment` service provides the core abstraction for working with environments.</span></span> <span data-ttu-id="5d7a1-173">Questo servizio viene fornito da ASP.NET hosting dei livelli e possono essere inserite nella logica di avvio tramite [Dependency Injection](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-173">This service is provided by the ASP.NET hosting layer, and can be injected into your startup logic via [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="5d7a1-174">Il modello di sito web ASP.NET Core in Visual Studio utilizza questo approccio per caricare i file di configurazione specifici dell'ambiente (se presente) e per personalizzare le impostazioni di gestione degli errori dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-174">The ASP.NET Core web site template in Visual Studio uses this approach to load environment-specific configuration files (if present) and to customize the app's error handling settings.</span></span> <span data-ttu-id="5d7a1-175">In entrambi i casi, questo comportamento viene ottenuto facendo riferimento all'ambiente specificato chiamando `EnvironmentName` o `IsEnvironment` nell'istanza di `IHostingEnvironment` passato al metodo appropriato.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-175">In both cases, this behavior is achieved by referring to the currently specified environment by calling `EnvironmentName` or `IsEnvironment` on the instance of `IHostingEnvironment` passed into the appropriate method.</span></span>

> [!NOTE]
> <span data-ttu-id="5d7a1-176">Se è necessario verificare se l'applicazione è in esecuzione in un ambiente specifico, utilizzare `env.IsEnvironment("environmentname")` poiché correttamente lo ignora maiuscole (invece di controllare se `env.EnvironmentName == "Development"` ad esempio).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-176">If you need to check whether the application is running in a particular environment, use `env.IsEnvironment("environmentname")` since it will correctly ignore case (instead of checking if `env.EnvironmentName == "Development"` for example).</span></span>

<span data-ttu-id="5d7a1-177">Ad esempio, è possibile utilizzare il codice seguente nel metodo di configurazione per la gestione degli errori specifici di ambiente del programma di installazione:</span><span class="sxs-lookup"><span data-stu-id="5d7a1-177">For example, you can use the following code in your Configure method to setup environment specific error handling:</span></span>

<span data-ttu-id="5d7a1-178">[!code-csharp[Principale](environments/sample/src/Environments/Startup.cs?range=19-30)]</span><span class="sxs-lookup"><span data-stu-id="5d7a1-178">[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]</span></span>

<span data-ttu-id="5d7a1-179">Se l'app è in esecuzione in un `Development` ambiente, quindi si abilita il supporto di runtime necessario per utilizzare la funzionalità "BrowserLink" in Visual Studio, le pagine di errore specifico di sviluppo (che in genere non devono essere eseguite nell'ambiente di produzione) ed errore di database speciale pagine (che forniscono un modo per applicare le migrazioni e devono pertanto essere utilizzate solo in fase di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-179">If the app is running in a `Development` environment, then it enables the runtime support necessary to use the "BrowserLink" feature in Visual Studio, development-specific error pages (which typically should not be run in production) and special database error pages (which provide a way to apply migrations and should therefore only be used in development).</span></span> <span data-ttu-id="5d7a1-180">In caso contrario, se l'app non è in esecuzione in un ambiente di sviluppo, una pagina di gestione degli errori standard è configurata per essere visualizzato in risposta a eventuali eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-180">Otherwise, if the app is not running in a development environment, a standard error handling page is configured to be displayed in response to any unhandled exceptions.</span></span>

<span data-ttu-id="5d7a1-181">Potrebbe essere necessario determinare quale contenuto da inviare al client in fase di esecuzione, a seconda dell'ambiente corrente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-181">You may need to determine which content to send to the client at runtime, depending on the current environment.</span></span> <span data-ttu-id="5d7a1-182">Ad esempio, in un ambiente di sviluppo si forniscono in genere non ridotta a icona script e fogli di stile, che rende il debug.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-182">For example, in a development environment you generally serve non-minimized scripts and style sheets, which makes debugging easier.</span></span> <span data-ttu-id="5d7a1-183">Ambienti di produzione e di test devono essere utilizzato per le versioni minimizzate e in genere da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-183">Production and test environments should serve the minified versions and generally from a CDN.</span></span> <span data-ttu-id="5d7a1-184">È possibile farlo usando l'ambiente [helper di tag](../mvc/views/tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-184">You can do this using the Environment [tag helper](../mvc/views/tag-helpers/intro.md).</span></span> <span data-ttu-id="5d7a1-185">L'helper di tag di ambiente restituirà il relativo contenuto solo se l'ambiente corrente corrisponde a uno degli ambienti specificati utilizzando il `names` attributo.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-185">The Environment tag helper will only render its contents if the current environment matches one of the environments specified using the `names` attribute.</span></span>

<span data-ttu-id="5d7a1-186">[!code-html[Principale](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]</span><span class="sxs-lookup"><span data-stu-id="5d7a1-186">[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]</span></span>

<span data-ttu-id="5d7a1-187">Per iniziare a utilizzare gli helper di tag nell'applicazione, vedere [introduzione per gli helper di Tag](../mvc/views/tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-187">To get started with using tag helpers in your application see [Introduction to Tag Helpers](../mvc/views/tag-helpers/intro.md).</span></span>

## <a name="startup-conventions"></a><span data-ttu-id="5d7a1-188">Convenzioni di avvio</span><span class="sxs-lookup"><span data-stu-id="5d7a1-188">Startup conventions</span></span>

<span data-ttu-id="5d7a1-189">ASP.NET di base supporta un approccio basato sulle convenzioni per la configurazione di avvio di un'applicazione in base all'ambiente corrente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-189">ASP.NET Core supports a convention-based approach to configuring an application's startup based on the current environment.</span></span> <span data-ttu-id="5d7a1-190">Anche a livello di codice, è possibile controllare il comportamento dell'applicazione in base a quale ambiente è in, che consente di creare e gestire le convenzioni.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-190">You can also programmatically control how your application behaves according to which environment it is in, allowing you to create and manage your own conventions.</span></span>

<span data-ttu-id="5d7a1-191">Quando si avvia un'applicazione ASP.NET di base, il `Startup` classe viene utilizzata per avviare l'applicazione, caricare il impostazioni di configurazione e così via ([ulteriori informazioni sull'avvio di ASP.NET](startup.md)).</span><span class="sxs-lookup"><span data-stu-id="5d7a1-191">When an ASP.NET Core application starts, the `Startup` class is used to bootstrap the application, load its configuration settings, etc. ([learn more about ASP.NET startup](startup.md)).</span></span> <span data-ttu-id="5d7a1-192">Tuttavia, se esiste una classe denominata `Startup{EnvironmentName}` (ad esempio `StartupDevelopment`) e `ASPNETCORE_ENVIRONMENT` variabile di ambiente corrispondente, tale nome sarà quello `Startup` classe viene invece utilizzata.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-192">However, if a class exists named `Startup{EnvironmentName}` (for example `StartupDevelopment`), and the `ASPNETCORE_ENVIRONMENT` environment variable matches that name, then that `Startup` class is used instead.</span></span> <span data-ttu-id="5d7a1-193">Di conseguenza, è possibile configurare `Startup` per lo sviluppo, ma disporre di un' `StartupProduction` che viene utilizzata quando si esegue l'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-193">Thus, you could configure `Startup` for development, but have a separate `StartupProduction` that would be used when the app is run in production.</span></span> <span data-ttu-id="5d7a1-194">O viceversa.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-194">Or vice versa.</span></span>

> [!NOTE]
> <span data-ttu-id="5d7a1-195">La chiamata `WebHostBuilder.UseStartup<TStartup>()` esegue l'override delle sezioni di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-195">Calling `WebHostBuilder.UseStartup<TStartup>()` overrides configuration sections.</span></span>

<span data-ttu-id="5d7a1-196">Oltre a utilizzare un completamente separate `Startup` classe in base all'ambiente corrente, è anche possibile apportare modifiche di configurazione dell'applicazione all'interno di un `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-196">In addition to using an entirely separate `Startup` class based on the current environment, you can also make adjustments to how the application is configured within a `Startup` class.</span></span> <span data-ttu-id="5d7a1-197">Il `Configure()` e `ConfigureServices()` metodi supportano le versioni specifiche dell'ambiente simile al `Startup` classe stessa, nel formato `Configure{EnvironmentName}()` e `Configure{EnvironmentName}Services()`.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-197">The `Configure()` and `ConfigureServices()` methods support environment-specific versions similar to the `Startup` class itself, of the form `Configure{EnvironmentName}()` and `Configure{EnvironmentName}Services()`.</span></span> <span data-ttu-id="5d7a1-198">Se si definisce un metodo `ConfigureDevelopment()` verrà chiamato anziché `Configure()` quando l'ambiente è impostata per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-198">If you define a method `ConfigureDevelopment()` it will be called instead of `Configure()` when the environment is set to development.</span></span> <span data-ttu-id="5d7a1-199">Analogamente, `ConfigureDevelopmentServices()` deve essere chiamato anziché `ConfigureServices()` nello stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-199">Likewise, `ConfigureDevelopmentServices()` would be called instead of `ConfigureServices()` in the same environment.</span></span>

## <a name="summary"></a><span data-ttu-id="5d7a1-200">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="5d7a1-200">Summary</span></span>

<span data-ttu-id="5d7a1-201">ASP.NET Core fornisce una serie di funzionalità e le convenzioni che consentono agli sviluppatori di controllare facilmente il comportamento delle applicazioni in ambienti diversi.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-201">ASP.NET Core provides a number of features and conventions that allow developers to easily control how their applications behave in different environments.</span></span> <span data-ttu-id="5d7a1-202">Quando si pubblica un'applicazione dallo sviluppo alla gestione temporanea alla produzione, set di variabili di ambiente in modo appropriato per l'ambiente di consentire per l'ottimizzazione dell'applicazione per l'utilizzo di debug, test o di produzione, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="5d7a1-202">When publishing an application from development to staging to production, environment variables set appropriately for the environment allow for optimization of the application for debugging, testing, or production use, as appropriate.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d7a1-203">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5d7a1-203">Additional Resources</span></span>

* [<span data-ttu-id="5d7a1-204">Configurazione</span><span class="sxs-lookup"><span data-stu-id="5d7a1-204">Configuration</span></span>](configuration.md)

* [<span data-ttu-id="5d7a1-205">Introduzione agli helper di Tag</span><span class="sxs-lookup"><span data-stu-id="5d7a1-205">Introduction to Tag Helpers</span></span>](../mvc/views/tag-helpers/intro.md)