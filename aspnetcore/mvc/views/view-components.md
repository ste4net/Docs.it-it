---
title: Visualizza i componenti
author: rick-anderson
description: Visualizza i componenti sono destinati in qualsiasi punto che si dispone di logica di rendering riutilizzabili.
keywords: ASP.NET Core, Visualizza i componenti, visualizzazione parziale
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
ms.openlocfilehash: 68efa1f313c73e2aee3e0e3c2fcb9eba0e12a62f
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2017
---
# <a name="view-components"></a><span data-ttu-id="5f406-104">Visualizza i componenti</span><span class="sxs-lookup"><span data-stu-id="5f406-104">View components</span></span>

<span data-ttu-id="5f406-105">Da [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f406-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[<span data-ttu-id="5f406-106">Visualizzare o scaricare codice di esempio</span><span class="sxs-lookup"><span data-stu-id="5f406-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)

## <a name="introducing-view-components"></a><span data-ttu-id="5f406-107">Introduzione a componenti di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-107">Introducing view components</span></span>

<span data-ttu-id="5f406-108">Nuovo ad ASP.NET MVC di base, Visualizza i componenti sono simili alle visualizzazioni parziali, ma sono molto più potenti.</span><span class="sxs-lookup"><span data-stu-id="5f406-108">New to ASP.NET Core MVC, view components are similar to partial views, but they are much more powerful.</span></span> <span data-ttu-id="5f406-109">Visualizza i componenti non utilizzare l'associazione di modelli e dipendono solo i dati forniti durante la chiamata al suo interno.</span><span class="sxs-lookup"><span data-stu-id="5f406-109">View components don’t use model binding, and only depend on the data you provide when calling into it.</span></span> <span data-ttu-id="5f406-110">Un componente di visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="5f406-110">A view component:</span></span>

* <span data-ttu-id="5f406-111">Esegue il rendering di un blocco anziché un'intera risposta.</span><span class="sxs-lookup"><span data-stu-id="5f406-111">Renders a chunk rather than a whole response</span></span>
* <span data-ttu-id="5f406-112">Include gli stesso la separazione di problemi e i vantaggi di testabilità trovati tra un controller e una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-112">Includes the same separation-of-concerns and testability benefits found between a controller and view</span></span>
* <span data-ttu-id="5f406-113">Può avere parametri e logica di business</span><span class="sxs-lookup"><span data-stu-id="5f406-113">Can have parameters and business logic</span></span>
* <span data-ttu-id="5f406-114">In genere viene richiamato da una pagina di layout</span><span class="sxs-lookup"><span data-stu-id="5f406-114">Is typically invoked from a layout page</span></span>

<span data-ttu-id="5f406-115">Visualizza i componenti sono destinati in qualsiasi punto che si dispone di logica di rendering riutilizzabile che è troppo complessa per una visualizzazione parziale, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5f406-115">View components are intended anywhere you have reusable rendering logic that is too complex for a partial view, such as:</span></span>

* <span data-ttu-id="5f406-116">Menu di navigazione dinamica</span><span class="sxs-lookup"><span data-stu-id="5f406-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="5f406-117">Cloud tag (in cui viene eseguita una query del database)</span><span class="sxs-lookup"><span data-stu-id="5f406-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="5f406-118">Pannello di accesso</span><span class="sxs-lookup"><span data-stu-id="5f406-118">Login panel</span></span>
* <span data-ttu-id="5f406-119">Carrello acquisti</span><span class="sxs-lookup"><span data-stu-id="5f406-119">Shopping cart</span></span>
* <span data-ttu-id="5f406-120">Articoli pubblicati di recente</span><span class="sxs-lookup"><span data-stu-id="5f406-120">Recently published articles</span></span>
* <span data-ttu-id="5f406-121">Contenuto dell'intestazione laterale in un tipico blog</span><span class="sxs-lookup"><span data-stu-id="5f406-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="5f406-122">Un pannello di accesso che verrebbe eseguito in ogni pagina e visualizza entrambi i collegamenti per effettuare la disconnessione o di log, a seconda di log nello stato dell'utente</span><span class="sxs-lookup"><span data-stu-id="5f406-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="5f406-123">Un componente di visualizzazione è costituito da due parti: la classe (in genere derivato da [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) e il risultato restituisce (in genere una vista).</span><span class="sxs-lookup"><span data-stu-id="5f406-123">A view component consists of two parts: the class (typically derived from [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="5f406-124">Ad esempio controller, un componente di visualizzazione può essere un POCO, ma desidera sfruttare i metodi e le proprietà disponibili derivandolo dalla maggior parte degli sviluppatori `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="5f406-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="5f406-125">Creazione di un componente di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-125">Creating a view component</span></span>

<span data-ttu-id="5f406-126">In questa sezione contiene i requisiti di alto livello per creare un componente di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5f406-126">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="5f406-127">In un secondo momento nell'articolo, viene esaminare in dettaglio ogni passaggio e creare un componente di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5f406-127">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="5f406-128">La classe di componente di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-128">The view component class</span></span>

<span data-ttu-id="5f406-129">È possibile creare una classe di componente di visualizzazione da una delle seguenti:</span><span class="sxs-lookup"><span data-stu-id="5f406-129">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="5f406-130">Derivazione da *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="5f406-130">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="5f406-131">La decorazione di una classe con il `[ViewComponent]` attributo o deriva da una classe con il `[ViewComponent]` attributo</span><span class="sxs-lookup"><span data-stu-id="5f406-131">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="5f406-132">Creazione di una classe il cui nome termina con il suffisso *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="5f406-132">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="5f406-133">Ad esempio controller, Visualizza i componenti devono essere classi di pubbliche, non annidata e non astratta.</span><span class="sxs-lookup"><span data-stu-id="5f406-133">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="5f406-134">Il nome del componente di visualizzazione è il nome di classe con il suffisso "ViewComponent" rimosso.</span><span class="sxs-lookup"><span data-stu-id="5f406-134">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="5f406-135">È possibile inoltre specificare in modo esplicito utilizzando il `ViewComponentAttribute.Name` proprietà.</span><span class="sxs-lookup"><span data-stu-id="5f406-135">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="5f406-136">Una classe di componente di visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="5f406-136">A view component class:</span></span>

* <span data-ttu-id="5f406-137">Supporta completamente costruttore [inserimento di dipendenze](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="5f406-137">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="5f406-138">Non prendere parte del ciclo di vita di controller, ovvero non è possibile utilizzare [filtri](../controllers/filters.md) in un componente di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-138">Does not take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="5f406-139">Metodi del componente di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-139">View component methods</span></span>

<span data-ttu-id="5f406-140">Un componente di visualizzazione definisce la logica in un `InvokeAsync` metodo che restituisce un `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="5f406-140">A view component defines its logic in an `InvokeAsync` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="5f406-141">I parametri che provengano direttamente dalla chiamata di un componente di visualizzazione, non dall'associazione del modello.</span><span class="sxs-lookup"><span data-stu-id="5f406-141">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="5f406-142">Un componente di visualizzazione mai direttamente gestisce una richiesta.</span><span class="sxs-lookup"><span data-stu-id="5f406-142">A view component never directly handles a request.</span></span> <span data-ttu-id="5f406-143">In genere, Inizializza un modello e la passa a una visualizzazione chiamando un componente di visualizzazione di `View` metodo.</span><span class="sxs-lookup"><span data-stu-id="5f406-143">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="5f406-144">In sintesi, visualizzare i metodi del componente:</span><span class="sxs-lookup"><span data-stu-id="5f406-144">In summary, view component methods:</span></span>

* <span data-ttu-id="5f406-145">Definire un `InvokeAsync` metodo che restituisce un`IViewComponentResult`</span><span class="sxs-lookup"><span data-stu-id="5f406-145">Define an `InvokeAsync` method that returns an `IViewComponentResult`</span></span>
* <span data-ttu-id="5f406-146">Inizializza un modello e la passa a una vista mediante la chiamata in genere il `ViewComponent` `View` (metodo)</span><span class="sxs-lookup"><span data-stu-id="5f406-146">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method</span></span>
* <span data-ttu-id="5f406-147">Parametri provengono dal metodo di chiamata, non HTTP, non è disponibile alcuna associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="5f406-147">Parameters come from the calling method, not HTTP, there is no model binding</span></span>
* <span data-ttu-id="5f406-148">Sono non raggiungibile direttamente come un endpoint HTTP, vengono richiamati dal codice (in genere in una vista).</span><span class="sxs-lookup"><span data-stu-id="5f406-148">Are not reachable directly as an HTTP endpoint, they are invoked from your code (usually in a view).</span></span> <span data-ttu-id="5f406-149">Un componente di visualizzazione mai gestisce una richiesta</span><span class="sxs-lookup"><span data-stu-id="5f406-149">A view component never handles a request</span></span>
* <span data-ttu-id="5f406-150">La firma, anziché i dettagli della richiesta HTTP corrente sono sottoposti a overload</span><span class="sxs-lookup"><span data-stu-id="5f406-150">Are overloaded on the signature rather than any details from the current HTTP request</span></span>

### <a name="view-search-path"></a><span data-ttu-id="5f406-151">Percorso di ricerca di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-151">View search path</span></span>

<span data-ttu-id="5f406-152">Il runtime esegue la ricerca per la visualizzazione nei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5f406-152">The runtime searches for the view in the following paths:</span></span>

   * <span data-ttu-id="5f406-153">Viste /\<controller_name > /Components/\<view_component_name > /\<view_name ></span><span class="sxs-lookup"><span data-stu-id="5f406-153">Views/\<controller_name>/Components/\<view_component_name>/\<view_name></span></span>
   * <span data-ttu-id="5f406-154">Componenti/Shared/visualizzazioni/\<view_component_name > /\<view_name ></span><span class="sxs-lookup"><span data-stu-id="5f406-154">Views/Shared/Components/\<view_component_name>/\<view_name></span></span>

<span data-ttu-id="5f406-155">Il nome di visualizzazione predefinito per un componente di visualizzazione è *predefinito*, ovvero il file di visualizzazione in genere è denominato *cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5f406-155">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="5f406-156">È possibile specificare un nome di visualizzazione diverso quando si crea il risultato di componente di visualizzazione o quando si chiama il `View` metodo.</span><span class="sxs-lookup"><span data-stu-id="5f406-156">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="5f406-157">Si consiglia denominare il file di visualizzazione *cshtml* e utilizzare il *componenti/Shared/visualizzazioni/\<view_component_name > /\<view_name >* percorso.</span><span class="sxs-lookup"><span data-stu-id="5f406-157">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/\<view_component_name>/\<view_name>* path.</span></span> <span data-ttu-id="5f406-158">Il `PriorityList` del componente di visualizzazione utilizzato in questo esempio Usa *Views/Shared/Components/PriorityList/Default.cshtml* per la vista del componente di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5f406-158">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="5f406-159">Richiamo di un componente di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-159">Invoking a view component</span></span>

<span data-ttu-id="5f406-160">Per utilizzare il componente di visualizzazione, chiamare le operazioni seguenti all'interno di una vista:</span><span class="sxs-lookup"><span data-stu-id="5f406-160">To use the view component, call the following inside a view:</span></span>

```html
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

<span data-ttu-id="5f406-161">I parametri verranno passati al `InvokeAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="5f406-161">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="5f406-162">Il `PriorityList` sviluppato nell'articolo del componente di visualizzazione viene richiamato dal *Views/Todo/Index.cshtml* file della vista.</span><span class="sxs-lookup"><span data-stu-id="5f406-162">The `PriorityList` view component developed in the article is invoked from the *Views/Todo/Index.cshtml* view file.</span></span> <span data-ttu-id="5f406-163">Nell'esempio seguente, il `InvokeAsync` metodo viene chiamato con due parametri:</span><span class="sxs-lookup"><span data-stu-id="5f406-163">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

<span data-ttu-id="5f406-164">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span><span class="sxs-lookup"><span data-stu-id="5f406-164">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span></span>

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="5f406-165">Richiamo di un componente di visualizzazione come un Helper Tag</span><span class="sxs-lookup"><span data-stu-id="5f406-165">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="5f406-166">Per ASP.NET Core 1.1 e versioni successive, è possibile richiamare un componente di visualizzazione come una [Helper di Tag](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="5f406-166">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

<span data-ttu-id="5f406-167">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span><span class="sxs-lookup"><span data-stu-id="5f406-167">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span></span>

<span data-ttu-id="5f406-168">Base alla convezione Pascal classe e metodo parametri per gli helper di Tag vengono convertiti in loro [inferiore case kebab](http://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101#12273101).</span><span class="sxs-lookup"><span data-stu-id="5f406-168">Pascal-cased class and method parameters for Tag Helpers are translated into their [lower kebab case](http://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101#12273101).</span></span> <span data-ttu-id="5f406-169">Utilizza l'Helper di Tag per richiamare un componente di visualizzazione di `<vc></vc>` elemento.</span><span class="sxs-lookup"><span data-stu-id="5f406-169">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="5f406-170">Il componente di visualizzazione viene specificato come segue:</span><span class="sxs-lookup"><span data-stu-id="5f406-170">The view component is specified as follows:</span></span>

```html
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="5f406-171">Nota: Per utilizzare un componente di visualizzazione come un Helper Tag, è necessario registrare l'assembly contenente il componente di visualizzazione utilizzando il `@addTagHelper` direttiva.</span><span class="sxs-lookup"><span data-stu-id="5f406-171">Note: In order to use a View Component as a Tag Helper, you must register the assembly containing the View Component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="5f406-172">Ad esempio, se il componente di visualizzazione si trova in un assembly denominato "MyWebApp", aggiungere la seguente direttiva per il `_ViewImports.cshtml` file:</span><span class="sxs-lookup"><span data-stu-id="5f406-172">For example, if your View Component is in an assembly called "MyWebApp", add the following directive to the `_ViewImports.cshtml` file:</span></span>

```csharp
@addTagHelper *, MyWebApp
```

<span data-ttu-id="5f406-173">È possibile registrare un componente di visualizzazione come un Helper Tag a qualsiasi file che fa riferimento il componente di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5f406-173">You can register a View Component as a Tag Helper to any file that references the View Component.</span></span> <span data-ttu-id="5f406-174">Vedere [Gestione ambito Helper di Tag](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) per ulteriori informazioni su come registrare gli helper di Tag.</span><span class="sxs-lookup"><span data-stu-id="5f406-174">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="5f406-175">Il `InvokeAsync` metodo utilizzato in questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="5f406-175">The `InvokeAsync` method used in this tutorial:</span></span>

<span data-ttu-id="5f406-176">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span><span class="sxs-lookup"><span data-stu-id="5f406-176">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span></span>

<span data-ttu-id="5f406-177">Nel markup di Helper di Tag:</span><span class="sxs-lookup"><span data-stu-id="5f406-177">In Tag Helper markup:</span></span>

<span data-ttu-id="5f406-178">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span><span class="sxs-lookup"><span data-stu-id="5f406-178">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]</span></span>

<span data-ttu-id="5f406-179">Nell'esempio precedente, il `PriorityList` del componente di visualizzazione diventa `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="5f406-179">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="5f406-180">I parametri per il componente di visualizzazione vengono passati come attributi in lettere minuscole kebab.</span><span class="sxs-lookup"><span data-stu-id="5f406-180">The parameters to the view component are passed as attributes in lower kebab case.</span></span>

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="5f406-181">Richiamo di un componente di visualizzazione direttamente da un controller</span><span class="sxs-lookup"><span data-stu-id="5f406-181">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="5f406-182">Visualizza i componenti vengono in genere richiamati da una vista, ma è possibile richiamare tali direttamente da un metodo del controller.</span><span class="sxs-lookup"><span data-stu-id="5f406-182">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="5f406-183">Mentre i componenti di visualizzazione non definiscono endpoint come controller, è possibile implementare un'azione del controller che restituisce il contenuto di un `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="5f406-183">While view components do not define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="5f406-184">In questo esempio, il componente di visualizzazione viene chiamato direttamente dal controller:</span><span class="sxs-lookup"><span data-stu-id="5f406-184">In this example, the view component is called directly from the controller:</span></span>

<span data-ttu-id="5f406-185">[!code-csharp[Principale](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span><span class="sxs-lookup"><span data-stu-id="5f406-185">[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span></span>

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="5f406-186">Procedura dettagliata: Creazione di un componente di visualizzazione semplice</span><span class="sxs-lookup"><span data-stu-id="5f406-186">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="5f406-187">[Scaricare](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), compilare e testare il codice di avvio.</span><span class="sxs-lookup"><span data-stu-id="5f406-187">[Download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="5f406-188">Si tratta di un progetto semplice con un `Todo` controller che visualizza un elenco di *Todo* elementi.</span><span class="sxs-lookup"><span data-stu-id="5f406-188">It's a simple project with a `Todo` controller that displays a list of *Todo* items.</span></span>

![Elenco di ToDos](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="5f406-190">Aggiungere una classe ViewComponent</span><span class="sxs-lookup"><span data-stu-id="5f406-190">Add a ViewComponent class</span></span>

<span data-ttu-id="5f406-191">Creare un *ViewComponents* cartella e aggiungere le seguenti `PriorityListViewComponent` classe:</span><span class="sxs-lookup"><span data-stu-id="5f406-191">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

<span data-ttu-id="5f406-192">[!code-csharp[Principale](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="5f406-192">[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]</span></span>

<span data-ttu-id="5f406-193">Note sul codice:</span><span class="sxs-lookup"><span data-stu-id="5f406-193">Notes on the code:</span></span>

* <span data-ttu-id="5f406-194">Visualizzazione classi di componenti possono essere contenute in **qualsiasi** cartella nel progetto.</span><span class="sxs-lookup"><span data-stu-id="5f406-194">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="5f406-195">Poiché il nome della classe PriorityList**ViewComponent** termina con il suffisso **ViewComponent**, il runtime utilizza la stringa "PriorityList" quando si fa riferimento il componente della classe da una vista.</span><span class="sxs-lookup"><span data-stu-id="5f406-195">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="5f406-196">Verrà illustrato che in modo più dettagliato più avanti.</span><span class="sxs-lookup"><span data-stu-id="5f406-196">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="5f406-197">Il `[ViewComponent]` attributo è possibile modificare il nome utilizzato per fare riferimento a un componente di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5f406-197">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="5f406-198">Ad esempio, è possibile avere denominato la classe `XYZ` e applicati i `ViewComponent` attributo:</span><span class="sxs-lookup"><span data-stu-id="5f406-198">For example, we could have named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="5f406-199">Il `[ViewComponent]` attributo indica il selettore di componente di visualizzazione per utilizzare il nome `PriorityList` durante la ricerca per le visualizzazioni associate, il componente e di utilizzare la stringa "PriorityList" quando si fa riferimento il componente della classe da una vista.</span><span class="sxs-lookup"><span data-stu-id="5f406-199">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="5f406-200">Verrà illustrato che in modo più dettagliato più avanti.</span><span class="sxs-lookup"><span data-stu-id="5f406-200">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="5f406-201">Il componente utilizza [inserimento di dipendenze](../../fundamentals/dependency-injection.md) a disposizione il contesto dei dati.</span><span class="sxs-lookup"><span data-stu-id="5f406-201">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="5f406-202">`InvokeAsync`espone un metodo che può essere chiamato da una vista e può accettare un numero arbitrario di argomenti.</span><span class="sxs-lookup"><span data-stu-id="5f406-202">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="5f406-203">Il `InvokeAsync` il metodo restituisce il set di `ToDo` gli elementi che soddisfano il `isDone` e `maxPriority` parametri.</span><span class="sxs-lookup"><span data-stu-id="5f406-203">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="5f406-204">Creare la visualizzazione Razor componente di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-204">Create the view component Razor view</span></span>

* <span data-ttu-id="5f406-205">Creare il *componenti/Shared/visualizzazioni* cartella.</span><span class="sxs-lookup"><span data-stu-id="5f406-205">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="5f406-206">Questa cartella **deve** denominato *componenti*.</span><span class="sxs-lookup"><span data-stu-id="5f406-206">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="5f406-207">Creare il *viste/Shared/componenti/PriorityList* cartella.</span><span class="sxs-lookup"><span data-stu-id="5f406-207">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="5f406-208">Il nome della cartella deve corrispondere il nome della classe di componente di visualizzazione, o il nome della classe meno il suffisso (se è seguito convenzione e utilizzata la *ViewComponent* suffisso nel nome della classe).</span><span class="sxs-lookup"><span data-stu-id="5f406-208">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="5f406-209">Se è stata utilizzata la `ViewComponent` attributo, il nome della classe dovrà corrispondere la designazione di attributo.</span><span class="sxs-lookup"><span data-stu-id="5f406-209">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="5f406-210">Creare un *Views/Shared/Components/PriorityList/Default.cshtml* visualizzazione Razor: [!code-html [principale](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="5f406-210">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view: [!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span></span>
    
   <span data-ttu-id="5f406-211">La visualizzazione Razor accetta un elenco di `TodoItem` e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="5f406-211">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="5f406-212">Se il componente di visualizzazione `InvokeAsync` metodo non passa il nome della visualizzazione (come in questo esempio), *predefinito* per convenzione viene utilizzato per il nome della vista.</span><span class="sxs-lookup"><span data-stu-id="5f406-212">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="5f406-213">Più avanti nell'esercitazione verrà illustrato come passare il nome della vista.</span><span class="sxs-lookup"><span data-stu-id="5f406-213">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="5f406-214">Per ignorare lo stile predefinito per un controller specifico, aggiungere una visualizzazione nella cartella di visualizzazione controller specifico (ad esempio *Views/Todo/Components/PriorityList/Default.cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="5f406-214">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/Todo/Components/PriorityList/Default.cshtml)*.</span></span>
    
    <span data-ttu-id="5f406-215">Se il componente di visualizzazione è specifici dei controller, è possibile aggiungere alla cartella controller specifico (*Views/Todo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="5f406-215">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/Todo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="5f406-216">Aggiungere un `div` che contiene una chiamata per il componente alla fine dell'elenco di priorità il *Views/Todo/index.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="5f406-216">Add a `div` containing a call to the priority list component to the bottom of the *Views/Todo/index.cshtml* file:</span></span>

    <span data-ttu-id="5f406-217">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]</span><span class="sxs-lookup"><span data-stu-id="5f406-217">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]</span></span>

<span data-ttu-id="5f406-218">Il markup `@await Component.InvokeAsync` viene illustrata la sintassi per chiamare i componenti di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5f406-218">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="5f406-219">Il primo argomento è il nome del componente di cui che si desidera richiamare o chiamare.</span><span class="sxs-lookup"><span data-stu-id="5f406-219">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="5f406-220">I parametri successivi vengono passati al componente.</span><span class="sxs-lookup"><span data-stu-id="5f406-220">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="5f406-221">`InvokeAsync`può richiedere un numero arbitrario di argomenti.</span><span class="sxs-lookup"><span data-stu-id="5f406-221">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="5f406-222">Testare l'app.</span><span class="sxs-lookup"><span data-stu-id="5f406-222">Test the app.</span></span> <span data-ttu-id="5f406-223">La figura seguente mostra l'elenco di attività e gli elementi con priorità:</span><span class="sxs-lookup"><span data-stu-id="5f406-223">The following image shows the ToDo list and the priority items:</span></span>

![elementi di elenco e priorità attività](view-components/_static/pi.png)

<span data-ttu-id="5f406-225">È inoltre possibile chiamare il componente di visualizzazione direttamente dal controller:</span><span class="sxs-lookup"><span data-stu-id="5f406-225">You can also call the view component directly from the controller:</span></span>

<span data-ttu-id="5f406-226">[!code-csharp[Principale](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span><span class="sxs-lookup"><span data-stu-id="5f406-226">[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]</span></span>

![elementi con priorità dall'azione IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="5f406-228">Specificare un nome di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-228">Specifying a view name</span></span>

<span data-ttu-id="5f406-229">Un componente di visualizzazione complesso potrebbe essere necessario specificare una vista non predefinita in alcune condizioni.</span><span class="sxs-lookup"><span data-stu-id="5f406-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="5f406-230">Il codice seguente viene illustrato come specificare la vista "PVC" dal `InvokeAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="5f406-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="5f406-231">Aggiornamento di `InvokeAsync` metodo la `PriorityListViewComponent` classe.</span><span class="sxs-lookup"><span data-stu-id="5f406-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

<span data-ttu-id="5f406-232">[!code-csharp[Principale](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]</span><span class="sxs-lookup"><span data-stu-id="5f406-232">[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]</span></span>

<span data-ttu-id="5f406-233">Copia il *Views/Shared/Components/PriorityList/Default.cshtml* file da una vista denominata *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5f406-233">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="5f406-234">Aggiungere un'intestazione per indicare che la vista PVC è in uso.</span><span class="sxs-lookup"><span data-stu-id="5f406-234">Add a heading to indicate the PVC view is being used.</span></span>

<span data-ttu-id="5f406-235">[!code-html[Principale](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="5f406-235">[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]</span></span>

<span data-ttu-id="5f406-236">Aggiornamento *Views/TodoList/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5f406-236">Update *Views/TodoList/Index.cshtml*:</span></span>

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

<span data-ttu-id="5f406-237">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span><span class="sxs-lookup"><span data-stu-id="5f406-237">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]</span></span>

<span data-ttu-id="5f406-238">Eseguire l'app e verificare la visualizzazione PVC.</span><span class="sxs-lookup"><span data-stu-id="5f406-238">Run the app and verify PVC view.</span></span>

![Priorità del componente di visualizzazione](view-components/_static/pvc.png)

<span data-ttu-id="5f406-240">Se non viene visualizzata la vista PVC, verificare che si sta chiamando il componente di visualizzazione con una priorità pari a 4 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="5f406-240">If the PVC view is not rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="5f406-241">Esaminare il percorso di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5f406-241">Examine the view path</span></span>

* <span data-ttu-id="5f406-242">Modificare il parametro di priorità a tre o meno in modo non viene restituita la visualizzazione priority.</span><span class="sxs-lookup"><span data-stu-id="5f406-242">Change the priority parameter to three or less so the priority view is not returned.</span></span>
* <span data-ttu-id="5f406-243">Rinominare temporaneamente il *Views/Todo/Components/PriorityList/Default.cshtml* a *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5f406-243">Temporarily rename the *Views/Todo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="5f406-244">Testare l'app, si otterrà l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="5f406-244">Test the app, you'll get the following error:</span></span>

   <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="5f406-245">Copia *Views/Todo/Components/PriorityList/1Default.cshtml* a *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5f406-245">Copy *Views/Todo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="5f406-246">Aggiungere codice per il *Shared* Todo visualizzazione componente per indicare la vista è dal *Shared* cartella.</span><span class="sxs-lookup"><span data-stu-id="5f406-246">Add some markup to the *Shared* Todo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="5f406-247">Test di **Shared** vista del componente.</span><span class="sxs-lookup"><span data-stu-id="5f406-247">Test the **Shared** component view.</span></span>

![Output di attività con vista del componente condiviso](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a><span data-ttu-id="5f406-249">Evitare stringhe chiave</span><span class="sxs-lookup"><span data-stu-id="5f406-249">Avoiding magic strings</span></span>

<span data-ttu-id="5f406-250">Se si desidera compilare la sicurezza di tempo, è possibile sostituire il nome del componente di visualizzazione a livello di codice con il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="5f406-250">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="5f406-251">Creare il componente di visualizzazione senza il suffisso "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="5f406-251">Create the view component without the "ViewComponent" suffix:</span></span>

<span data-ttu-id="5f406-252">[!code-csharp[Principale](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]</span><span class="sxs-lookup"><span data-stu-id="5f406-252">[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]</span></span>

<span data-ttu-id="5f406-253">Aggiungere un `using` istruzione per il Razor consente di visualizzare file e utilizzare il `nameof` operatore:</span><span class="sxs-lookup"><span data-stu-id="5f406-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

<span data-ttu-id="5f406-254">[!code-html[Principale](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]</span><span class="sxs-lookup"><span data-stu-id="5f406-254">[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f406-255">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5f406-255">Additional Resources</span></span>

* [<span data-ttu-id="5f406-256">Inserimento di dipendenze nelle viste</span><span class="sxs-lookup"><span data-stu-id="5f406-256">Dependency injection into views</span></span>](dependency-injection.md)