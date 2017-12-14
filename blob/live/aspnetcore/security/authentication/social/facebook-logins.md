---
title: Configurazione di account di accesso esterno Facebook in ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra l'integrazione dell'autenticazione di Facebook account utente in un'applicazione ASP.NET di base esistente.
keywords: ASP.NET Core, Facebook, account di accesso, autenticazione
ms.author: riande
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.assetid: 8c65179b-688c-4af1-8f5e-1862920cda95
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/facebook-logins
ms.openlocfilehash: 826ac826c22dae81e5dbea08a11a62cac0b1068a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-facebook-authentication"></a><span data-ttu-id="bf297-104">Configurazione dell'autenticazione di Facebook</span><span class="sxs-lookup"><span data-stu-id="bf297-104">Configuring Facebook authentication</span></span>

<span data-ttu-id="bf297-105">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bf297-105">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bf297-106">In questa esercitazione viene illustrato come consentire agli utenti di accedere con il proprio account Facebook tramite un progetto ASP.NET Core 2.0 di esempio creato nel [pagina precedente](index.md).</span><span class="sxs-lookup"><span data-stu-id="bf297-106">This tutorial shows you how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](index.md).</span></span> <span data-ttu-id="bf297-107">Viene innanzitutto creato un ID App Facebook seguendo il [passaggi ufficiali](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="bf297-107">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="bf297-108">Creare l'app in Facebook</span><span class="sxs-lookup"><span data-stu-id="bf297-108">Create the app in Facebook</span></span>

*  <span data-ttu-id="bf297-109">Passare il [Facebook per gli sviluppatori](https://developers.facebook.com) pagina ed eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="bf297-109">Navigate to the [Facebook for Developers](https://developers.facebook.com) page and sign in.</span></span> <span data-ttu-id="bf297-110">Se si dispone già di un account Facebook, utilizzare il **iscriversi a Facebook** collegamento nella pagina di accesso per crearne uno.</span><span class="sxs-lookup"><span data-stu-id="bf297-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="bf297-111">Toccare il **creare App** pulsante nell'angolo superiore destro per creare un nuovo ID di App.</span><span class="sxs-lookup"><span data-stu-id="bf297-111">Tap the **Create App** button in the upper right corner to create a new App ID.</span></span>

   ![Facebook per portale sviluppatori aprire in Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="bf297-113">Compilare il modulo e toccare il **Crea ID App** pulsante.</span><span class="sxs-lookup"><span data-stu-id="bf297-113">Fill out the form and tap the **Create App ID** button.</span></span>

   ![Creare un nuovo ID di App](index/_static/FBNewAppId.png)

* <span data-ttu-id="bf297-115">Quando verrà visualizzata **selezionare un prodotto** prompt dei comandi, fare clic su **Set Up** sul **account Facebook** scheda.</span><span class="sxs-lookup"><span data-stu-id="bf297-115">When presented with **Select a product** prompt, Click **Set Up** on the **Facebook Login** card.</span></span>

   ![Pagina di installazione del prodotto](index/_static/FBProductSetup.png)

* <span data-ttu-id="bf297-117">Il **delle Guide rapide** guidata consentirà di avviare con **scegliere una piattaforma** come la prima pagina.</span><span class="sxs-lookup"><span data-stu-id="bf297-117">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="bf297-118">Fare clic su ignorare la procedura guidata per il momento di **impostazioni** collegamento nel menu a sinistra:</span><span class="sxs-lookup"><span data-stu-id="bf297-118">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

   ![Guida introduttiva a Skip](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="bf297-120">Viene visualizzata la **impostazioni OAuth Client** pagina:</span><span class="sxs-lookup"><span data-stu-id="bf297-120">You are presented with the **Client OAuth Settings** page:</span></span>

![Pagina Impostazioni OAuth client](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="bf297-122">Immettere l'URI di sviluppo con */signin-facebook* aggiunti al **URI di reindirizzamento di OAuth validi** campo (ad esempio: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="bf297-122">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="bf297-123">L'autenticazione di Facebook configurato più avanti in questa esercitazione consente di gestire automaticamente le richieste nel */signin-facebook* route per implementare il flusso di OAuth.</span><span class="sxs-lookup"><span data-stu-id="bf297-123">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

* <span data-ttu-id="bf297-124">Fare clic su **salvare modifiche**.</span><span class="sxs-lookup"><span data-stu-id="bf297-124">Click **Save Changes**.</span></span>

* <span data-ttu-id="bf297-125">Fare clic su di **Dashboard** collegamento nel riquadro di spostamento a sinistra.</span><span class="sxs-lookup"><span data-stu-id="bf297-125">Click the **Dashboard** link in the left navigation.</span></span> 

    <span data-ttu-id="bf297-126">In questa pagina, annotare il `App ID` e `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="bf297-126">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="bf297-127">Nella sezione successiva verranno aggiunti entrambi nell'applicazione ASP.NET di base:</span><span class="sxs-lookup"><span data-stu-id="bf297-127">You will add both into your ASP.NET Core application in the next section:</span></span>

   ![Dashboard dello sviluppatore di Facebook](index/_static/FBDashboard.png)

* <span data-ttu-id="bf297-129">Quando si distribuisce il sito è necessario rivedere il **account Facebook** pagina di installazione e registrare un nuovo URI pubblico.</span><span class="sxs-lookup"><span data-stu-id="bf297-129">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="bf297-130">Archiviare ID App Facebook e segreto dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="bf297-130">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="bf297-131">Collegare le impostazioni sensibili, ad esempio Facebook `App ID` e `App Secret` all'applicazione mediante configurazione di [Manager segreto](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="bf297-131">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="bf297-132">Ai fini di questa esercitazione, denominare il token `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="bf297-132">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

<span data-ttu-id="bf297-133">Eseguire i comandi seguenti per archiviare in modo sicuro `App ID` e `App Secret` utilizzando Gestione segreto:</span><span class="sxs-lookup"><span data-stu-id="bf297-133">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="bf297-134">Configurare l'autenticazione di Facebook</span><span class="sxs-lookup"><span data-stu-id="bf297-134">Configure Facebook Authentication</span></span>

<span data-ttu-id="bf297-135">Il modello di progetto utilizzato in questa esercitazione assicura che [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) pacchetto è già installato.</span><span class="sxs-lookup"><span data-stu-id="bf297-135">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package is already installed.</span></span>

* <span data-ttu-id="bf297-136">Per installare il pacchetto con Visual Studio 2017, fare clic sul progetto e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="bf297-136">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="bf297-137">Per installare con .NET Core CLI, eseguire le operazioni seguenti nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="bf297-137">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="bf297-138">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="bf297-138">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="bf297-139">Aggiungere il servizio di Facebook nel `ConfigureServices` metodo il *Startup.cs* file:</span><span class="sxs-lookup"><span data-stu-id="bf297-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="bf297-140">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="bf297-140">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="bf297-141">Aggiungere il middleware di Facebook nel `Configure` metodo *Startup.cs* file:</span><span class="sxs-lookup"><span data-stu-id="bf297-141">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

<span data-ttu-id="bf297-142">Vedere il [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) riferimento API per altre informazioni sulle opzioni di configurazione supportati dall'autenticazione di Facebook.</span><span class="sxs-lookup"><span data-stu-id="bf297-142">See the [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="bf297-143">Opzioni di configurazione possono essere utilizzate per:</span><span class="sxs-lookup"><span data-stu-id="bf297-143">Configuration options can be used to:</span></span>

* <span data-ttu-id="bf297-144">Richiedere informazioni diverse relative all'utente.</span><span class="sxs-lookup"><span data-stu-id="bf297-144">Request different information about the user.</span></span>
* <span data-ttu-id="bf297-145">Aggiungere gli argomenti di stringa di query per personalizzare l'esperienza di accesso.</span><span class="sxs-lookup"><span data-stu-id="bf297-145">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="bf297-146">Accedere con Facebook</span><span class="sxs-lookup"><span data-stu-id="bf297-146">Sign in with Facebook</span></span>

<span data-ttu-id="bf297-147">Eseguire l'applicazione e fare clic su **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="bf297-147">Run your application and click **Log in**.</span></span> <span data-ttu-id="bf297-148">Viene visualizzata un'opzione per accedere con Facebook.</span><span class="sxs-lookup"><span data-stu-id="bf297-148">You see an option to sign in with Facebook.</span></span>

![Applicazione Web: utente non autenticato](index/_static/DoneFacebook.png)

<span data-ttu-id="bf297-150">Facendo clic sul **Facebook**, si verrà reindirizzati a Facebook per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="bf297-150">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Pagina di autenticazione di Facebook](index/_static/FBLogin.png)

<span data-ttu-id="bf297-152">Indirizzo di posta elettronica e profilo pubblico le richieste di autenticazione di Facebook per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="bf297-152">Facebook authentication requests public profile and email address by default:</span></span>

![Pagina di autenticazione di Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="bf297-154">Dopo aver immesso le credenziali di Facebook, che si verrà reindirizzati al sito in cui è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="bf297-154">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="bf297-155">A questo punto si è connessi utilizzando le credenziali di Facebook:</span><span class="sxs-lookup"><span data-stu-id="bf297-155">You are now logged in using your Facebook credentials:</span></span>

![Applicazione Web: utente autenticato](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="bf297-157">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="bf297-157">Troubleshooting</span></span>

* <span data-ttu-id="bf297-158">**ASP.NET Core solo 2. x:** identità se non è configurato per la chiamata `services.AddIdentity` in `ConfigureServices`, il tentativo di autenticazione comporterà *ArgumentException: è necessario specificare l'opzione 'SignInScheme'*.</span><span class="sxs-lookup"><span data-stu-id="bf297-158">**ASP.NET Core 2.x only:** If Identity is not configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="bf297-159">Il modello di progetto utilizzato in questa esercitazione garantisce che questa operazione viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="bf297-159">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="bf297-160">Se il database del sito non è stato creato applicando la migrazione iniziale, è possibile ottenere *un'operazione di database non riuscita durante l'elaborazione della richiesta* errore.</span><span class="sxs-lookup"><span data-stu-id="bf297-160">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="bf297-161">Toccare **applicare le migrazioni** per creare il database e dell'aggiornamento per ignorare l'errore.</span><span class="sxs-lookup"><span data-stu-id="bf297-161">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bf297-162">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="bf297-162">Next steps</span></span>

* <span data-ttu-id="bf297-163">In questo articolo è stato illustrato come è possibile eseguire l'autenticazione con Facebook.</span><span class="sxs-lookup"><span data-stu-id="bf297-163">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="bf297-164">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](index.md).</span><span class="sxs-lookup"><span data-stu-id="bf297-164">You can follow a similar approach to authenticate with other providers listed on the [previous page](index.md).</span></span>

* <span data-ttu-id="bf297-165">Quando si pubblica il sito web all'app web di Azure, è consigliabile reimpostare il `AppSecret` nel portale per sviluppatori di Facebook.</span><span class="sxs-lookup"><span data-stu-id="bf297-165">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="bf297-166">Impostare il `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` come impostazioni dell'applicazione nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="bf297-166">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="bf297-167">Il sistema di configurazione è impostare lettura delle chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="bf297-167">The configuration system is set up to read keys from environment variables.</span></span>