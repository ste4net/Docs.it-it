<span data-ttu-id="8a54a-101">Nella tabella seguente sono specificati i parametri dei generatori di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8a54a-101">The following table details the ASP.NET Core code generators\` parameters:</span></span>

| <span data-ttu-id="8a54a-102">Parametro</span><span class="sxs-lookup"><span data-stu-id="8a54a-102">Parameter</span></span>               | <span data-ttu-id="8a54a-103">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8a54a-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="8a54a-104">-m</span><span class="sxs-lookup"><span data-stu-id="8a54a-104">-m</span></span>  | <span data-ttu-id="8a54a-105">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="8a54a-105">The name of the model.</span></span> |
| <span data-ttu-id="8a54a-106">-dc</span><span class="sxs-lookup"><span data-stu-id="8a54a-106">-dc</span></span>  | <span data-ttu-id="8a54a-107">Contesto dati.</span><span class="sxs-lookup"><span data-stu-id="8a54a-107">The data context.</span></span> |
| <span data-ttu-id="8a54a-108">-udl</span><span class="sxs-lookup"><span data-stu-id="8a54a-108">-udl</span></span> | <span data-ttu-id="8a54a-109">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="8a54a-109">Use the default layout.</span></span> |
| <span data-ttu-id="8a54a-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="8a54a-110">-outDir</span></span> | <span data-ttu-id="8a54a-111">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="8a54a-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="8a54a-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="8a54a-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="8a54a-113">Aggiunge `_ValidationScriptsPartial` per modificare e creare pagine</span><span class="sxs-lookup"><span data-stu-id="8a54a-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="8a54a-114">Utilizzare il commutatore `h` per ottenere assistenza sul comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="8a54a-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="8a54a-115">Eseguire il test dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="8a54a-115">Test the app</span></span>

* <span data-ttu-id="8a54a-116">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8a54a-116">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="8a54a-117">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8a54a-117">Test the **Create** link.</span></span>

 ![Pagina Crea](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="8a54a-119">Eseguire il test dei collegamenti **Modifica**, **Dettagli** e **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="8a54a-119">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8a54a-120">Se viene visualizzato un errore simile al seguente, verificare di avere eseguito le migrazioni e di avere aggiornato il database:</span><span class="sxs-lookup"><span data-stu-id="8a54a-120">If you get the error similar to the following, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
'no such table: Movie'.
```