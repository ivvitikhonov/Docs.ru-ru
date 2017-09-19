---
title: "Объединение и Минификация в ASP.NET Core"
author: spboyer
description: 
keywords: "ASP.NET Core, объединение и Минификация, CSS, JavaScript, уменьшения, BuildBundlerMinifier"
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: d54230f9-8e5f-4861-a29c-1d3a14e0b0d9
ms.technology: aspnet
ms.prod: aspnet-core
uid: client-side/bundling-and-minification
ms.openlocfilehash: d8512bdd49b61019f22a49900bdd65086d821a6b
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2017
---
# <a name="bundling-and-minification-in-aspnet-core"></a><span data-ttu-id="08115-103">Объединение и Минификация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08115-103">Bundling and minification in ASP.NET Core</span></span>

<span data-ttu-id="08115-104">Объединение и Минификация — это два метода можно использовать в ASP.NET для повышения производительности загрузки страницы для веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="08115-104">Bundling and minification are two techniques you can use in ASP.NET to improve page load performance for your web application.</span></span> <span data-ttu-id="08115-105">Объединение объединяет несколько файлов в один файл.</span><span class="sxs-lookup"><span data-stu-id="08115-105">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="08115-106">Минификации выполняет различные оптимизации кода различные сценарии и CSS, что приводит в полезных данных меньшего размера.</span><span class="sxs-lookup"><span data-stu-id="08115-106">Minification performs a variety of different code optimizations to scripts and CSS, which results in smaller payloads.</span></span> <span data-ttu-id="08115-107">Использовать совместно, объединение и Минификация повышает производительность во время загрузки, уменьшая число запросов к серверу и уменьшения размера запрошенных ресурсов (например, файлов CSS и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="08115-107">Used together, bundling and minification improves load time performance by reducing the number of requests to the server and reducing the size of the requested assets (such as CSS and JavaScript files).</span></span>

<span data-ttu-id="08115-108">В этой статье объясняется преимущества использования объединение и Минификация, включая использование этих функций с приложениями ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08115-108">This article explains the benefits of using bundling and minification, including how these features can be used with ASP.NET Core applications.</span></span>

## <a name="overview"></a><span data-ttu-id="08115-109">Обзор</span><span class="sxs-lookup"><span data-stu-id="08115-109">Overview</span></span>

<span data-ttu-id="08115-110">В приложениях ASP.NET Core существует несколько вариантов объединение и Минификация ресурсами на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="08115-110">In ASP.NET Core apps, there are multiple options for bundling and minifying client-side resources.</span></span> <span data-ttu-id="08115-111">Основные шаблоны для MVC и предоставления решения с помощью файла конфигурации и пакет BuildBundlerMinifier NuGet out of box.</span><span class="sxs-lookup"><span data-stu-id="08115-111">The core templates for MVC provide an out-of-the-box solution using a configuration file and BuildBundlerMinifier NuGet package.</span></span> <span data-ttu-id="08115-112">Сторонние средства, такие как [Gulp](using-gulp.md) и [Grunt](using-grunt.md) также могут использоваться для выполнения тех же задач сложности и дополнительных рабочего процесса требуется процессу.</span><span class="sxs-lookup"><span data-stu-id="08115-112">Third party tools, such as [Gulp](using-gulp.md) and [Grunt](using-grunt.md) are also available to accomplish the same tasks should your processes require additional workflow or complexities.</span></span> <span data-ttu-id="08115-113">С помощью разработки объединение и Минификация, уменьшенный файлы создаются до развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="08115-113">By using design-time bundling and minification, the minified files are created prior to the application’s deployment.</span></span> <span data-ttu-id="08115-114">Объединение и Минификация перед развертыванием предоставляет преимущество снижение нагрузки.</span><span class="sxs-lookup"><span data-stu-id="08115-114">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="08115-115">Однако следует отметить, что объединение во время разработки и иными увеличивает сложность сборки и работает только с статических файлов.</span><span class="sxs-lookup"><span data-stu-id="08115-115">However, it’s important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

<span data-ttu-id="08115-116">Объединение и Минификация главным образом улучшить время загрузки первого запроса страницы.</span><span class="sxs-lookup"><span data-stu-id="08115-116">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="08115-117">После запросил веб-страницы браузер кэширует активы (JavaScript, CSS и изображения), объединение и Минификация не даст любое повышение производительности при запросе ту же страницу или сайт страниц на том же запросе же активы.</span><span class="sxs-lookup"><span data-stu-id="08115-117">Once a web page has been requested, the browser caches the assets (JavaScript, CSS and images) so bundling and minification won’t provide any performance boost when requesting the same page, or pages on the same site requesting the same assets.</span></span> <span data-ttu-id="08115-118">Если вы не задали заголовок правильно на Ваши активы истечения срока действия и не используйте объединение и Минификация, эвристики актуальность обозревателя будут отмечены как активы устаревших через несколько дней и требуется браузер запроса на проверку для каждого ресурса.</span><span class="sxs-lookup"><span data-stu-id="08115-118">If you don’t set the expires header correctly on your assets, and you don’t use bundling and minification, the browser's freshness heuristics will mark the assets stale after a few days and the browser will require a validation request for each asset.</span></span> <span data-ttu-id="08115-119">В этом случае объединение и Минификация обеспечивают повышение производительности даже после первого запроса страницы.</span><span class="sxs-lookup"><span data-stu-id="08115-119">In this case, bundling and minification provide a performance increase even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="08115-120">Объединение</span><span class="sxs-lookup"><span data-stu-id="08115-120">Bundling</span></span>

<span data-ttu-id="08115-121">Объединение — это компонент, упрощающий для объединения или объединить несколько файлов в один файл.</span><span class="sxs-lookup"><span data-stu-id="08115-121">Bundling is a feature that makes it easy to combine or bundle multiple files into a single file.</span></span> <span data-ttu-id="08115-122">Так как объединение объединяет несколько файлов в один файл, это уменьшает количество запросов к серверу, которые необходимы для получения и отображения web средств, таких как веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="08115-122">Because bundling combines multiple files into a single file, it reduces the number of requests to the server that are required to retrieve and display a web asset, such as a web page.</span></span> <span data-ttu-id="08115-123">Можно создать CSS, JavaScript и другие пакеты.</span><span class="sxs-lookup"><span data-stu-id="08115-123">You can create CSS, JavaScript and other bundles.</span></span> <span data-ttu-id="08115-124">Меньшее число файлов означает меньшее количество HTTP-запросов из браузера на сервер или из службы, предоставляющей приложение.</span><span class="sxs-lookup"><span data-stu-id="08115-124">Fewer files means fewer HTTP requests from your browser to the server or from the service providing your application.</span></span> <span data-ttu-id="08115-125">Результатом является повышение производительности загрузки первой страницы.</span><span class="sxs-lookup"><span data-stu-id="08115-125">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="08115-126">Минификации</span><span class="sxs-lookup"><span data-stu-id="08115-126">Minification</span></span>

<span data-ttu-id="08115-127">Минификации выполняет различные оптимизации различных кода для уменьшения размера запрошенных ресурсов (например, CSS, изображения, файлы JavaScript).</span><span class="sxs-lookup"><span data-stu-id="08115-127">Minification performs a variety of different code optimizations to reduce the size of requested assets (such as CSS, images, JavaScript files).</span></span> <span data-ttu-id="08115-128">Общие результаты минификации включают Удаление лишних пробелов и комментарии и сокращать имена переменных в один символ.</span><span class="sxs-lookup"><span data-stu-id="08115-128">Common results of minification include removing unnecessary white space and comments, and shortening variable names to one character.</span></span>

<span data-ttu-id="08115-129">Рассмотрим следующую функцию JavaScript:</span><span class="sxs-lookup"><span data-stu-id="08115-129">Consider the following JavaScript function:</span></span>

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
  ///<signature>
  ///<summary> Adds an alt tab to the image
  // </summary>
  //<param name="imgElement" type="String">The image selector.</param>
  //<param name="ContextForImage" type="String">The image context.</param>
  ///</signature>
  var imageElement = $(imageTagAndImageID, imageContext);
  imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

<span data-ttu-id="08115-130">После минификации функция сокращается до следующее:</span><span class="sxs-lookup"><span data-stu-id="08115-130">After minification, the function is reduced to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="08115-131">Помимо удаление комментариев и ненужные пробелы, следующие параметры и имена переменных были переименованы (сокращено) следующим образом:</span><span class="sxs-lookup"><span data-stu-id="08115-131">In addition to removing the comments and unnecessary whitespace, the following parameters and variable names were renamed (shortened) as follows:</span></span>

<span data-ttu-id="08115-132">До преобразования</span><span class="sxs-lookup"><span data-stu-id="08115-132">Original</span></span> | <span data-ttu-id="08115-133">Переименовано</span><span class="sxs-lookup"><span data-stu-id="08115-133">Renamed</span></span>
--- | :---:
<span data-ttu-id="08115-134">imageTagAndImageID</span><span class="sxs-lookup"><span data-stu-id="08115-134">imageTagAndImageID</span></span> | <span data-ttu-id="08115-135">т</span><span class="sxs-lookup"><span data-stu-id="08115-135">t</span></span>
<span data-ttu-id="08115-136">в пределах изображения</span><span class="sxs-lookup"><span data-stu-id="08115-136">imageContext</span></span> | <span data-ttu-id="08115-137">пример</span><span class="sxs-lookup"><span data-stu-id="08115-137">a</span></span>
<span data-ttu-id="08115-138">imageElement</span><span class="sxs-lookup"><span data-stu-id="08115-138">imageElement</span></span> | <span data-ttu-id="08115-139">r</span><span class="sxs-lookup"><span data-stu-id="08115-139">r</span></span>

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="08115-140">Влияние объединение и Минификация</span><span class="sxs-lookup"><span data-stu-id="08115-140">Impact of bundling and minification</span></span>

<span data-ttu-id="08115-141">В следующей таблице показаны несколько важных различий между все активы по отдельности и объединение и Минификация простой веб-страницы.</span><span class="sxs-lookup"><span data-stu-id="08115-141">The following table shows several important differences between listing all the assets individually and using bundling and minification on a simple web page:</span></span>

<span data-ttu-id="08115-142">Действие</span><span class="sxs-lookup"><span data-stu-id="08115-142">Action</span></span> | <span data-ttu-id="08115-143">С B и M</span><span class="sxs-lookup"><span data-stu-id="08115-143">With B/M</span></span> | <span data-ttu-id="08115-144">Без B и M</span><span class="sxs-lookup"><span data-stu-id="08115-144">Without B/M</span></span> | <span data-ttu-id="08115-145">Изменение</span><span class="sxs-lookup"><span data-stu-id="08115-145">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="08115-146">Запросы файлов</span><span class="sxs-lookup"><span data-stu-id="08115-146">File Requests</span></span> |<span data-ttu-id="08115-147">7</span><span class="sxs-lookup"><span data-stu-id="08115-147">7</span></span> | <span data-ttu-id="08115-148">18</span><span class="sxs-lookup"><span data-stu-id="08115-148">18</span></span> | <span data-ttu-id="08115-149">157%</span><span class="sxs-lookup"><span data-stu-id="08115-149">157%</span></span>
<span data-ttu-id="08115-150">Передать КБ</span><span class="sxs-lookup"><span data-stu-id="08115-150">KB Transferred</span></span> | <span data-ttu-id="08115-151">156</span><span class="sxs-lookup"><span data-stu-id="08115-151">156</span></span> | <span data-ttu-id="08115-152">264.68</span><span class="sxs-lookup"><span data-stu-id="08115-152">264.68</span></span> | <span data-ttu-id="08115-153">70%</span><span class="sxs-lookup"><span data-stu-id="08115-153">70%</span></span>
<span data-ttu-id="08115-154">Время загрузки (мс)</span><span class="sxs-lookup"><span data-stu-id="08115-154">Load Time (MS)</span></span> | <span data-ttu-id="08115-155">885</span><span class="sxs-lookup"><span data-stu-id="08115-155">885</span></span> | <span data-ttu-id="08115-156">2360</span><span class="sxs-lookup"><span data-stu-id="08115-156">2360</span></span> | <span data-ttu-id="08115-157">167%</span><span class="sxs-lookup"><span data-stu-id="08115-157">167%</span></span>

<span data-ttu-id="08115-158">Отправлено байт было значительное сокращение с объединением как браузеры довольно verbose с заголовки HTTP, которые применяются в запросах.</span><span class="sxs-lookup"><span data-stu-id="08115-158">The bytes sent had a significant reduction with bundling as browsers are fairly verbose with the HTTP headers that they apply on requests.</span></span> <span data-ttu-id="08115-159">Время загрузки показывает значительным усовершенствованием по, однако в этом примере была запущена локально.</span><span class="sxs-lookup"><span data-stu-id="08115-159">The load time shows a big improvement, however this example was run locally.</span></span> <span data-ttu-id="08115-160">Вы получите больше повысить производительность при помощи активы объединение и Минификация передаваемых по сети.</span><span class="sxs-lookup"><span data-stu-id="08115-160">You will get greater gains in performance when using bundling and minification with assets transferred over a network.</span></span>

## <a name="using-bundling-and-minification-in-a-project"></a><span data-ttu-id="08115-161">С помощью объединение и Минификация в проекте</span><span class="sxs-lookup"><span data-stu-id="08115-161">Using bundling and minification in a project</span></span>

<span data-ttu-id="08115-162">Шаблон проекта MVC предоставляет `bundleconfig.json` файл конфигурации, который определяет параметры для каждого пакета.</span><span class="sxs-lookup"><span data-stu-id="08115-162">The MVC project template provides a `bundleconfig.json` configuration file which defines the options for each bundle.</span></span> <span data-ttu-id="08115-163">По умолчанию, определенное конфигурации один комплект для пользовательским кодом JavaScript (`wwwroot/js/site.js`) и таблицы стилей (`wwwroot/css/site.css`) файлы.</span><span class="sxs-lookup"><span data-stu-id="08115-163">By default, a single bundle configuration is defined for the custom JavaScript (`wwwroot/js/site.js`) and Stylesheet (`wwwroot/css/site.css`) files.</span></span>

<span data-ttu-id="08115-164">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]</span><span class="sxs-lookup"><span data-stu-id="08115-164">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]</span></span>

<span data-ttu-id="08115-165">Параметры пакета:</span><span class="sxs-lookup"><span data-stu-id="08115-165">Bundle options include:</span></span>

* <span data-ttu-id="08115-166">outputFileName - имя файла пакета для вывода.</span><span class="sxs-lookup"><span data-stu-id="08115-166">outputFileName - name of the bundle file to output.</span></span> <span data-ttu-id="08115-167">Может содержать относительный путь от `bundleconfig.json` файла.</span><span class="sxs-lookup"><span data-stu-id="08115-167">Can contain a relative path from the `bundleconfig.json` file.</span></span> <span data-ttu-id="08115-168">**Обязательно**</span><span class="sxs-lookup"><span data-stu-id="08115-168">**required**</span></span>
* <span data-ttu-id="08115-169">inputFiles - массив файлов, которые будут объединены.</span><span class="sxs-lookup"><span data-stu-id="08115-169">inputFiles - array of files to bundle together.</span></span> <span data-ttu-id="08115-170">Это относительные пути к файлу конфигурации.</span><span class="sxs-lookup"><span data-stu-id="08115-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="08115-171">**Необязательный**, * пустое значение преобразуется в пустой выходной файл.</span><span class="sxs-lookup"><span data-stu-id="08115-171">**optional**, *an empty value results in an empty output file.</span></span> <span data-ttu-id="08115-172">[Этот режим](http://www.tldp.org/LDP/abs/html/globbingref.html) поддерживаются шаблоны.</span><span class="sxs-lookup"><span data-stu-id="08115-172">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="08115-173">уменьшения - введите минификации параметры вывода.</span><span class="sxs-lookup"><span data-stu-id="08115-173">minify - minification options for the output type.</span></span> <span data-ttu-id="08115-174">**Необязательный**, *по умолчанию —`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="08115-174">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="08115-175">Параметры конфигурации доступны на тип выходного файла.</span><span class="sxs-lookup"><span data-stu-id="08115-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="08115-176">Уменьшитель CSS</span><span class="sxs-lookup"><span data-stu-id="08115-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="08115-177">Уменьшитель JavaScript</span><span class="sxs-lookup"><span data-stu-id="08115-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
    * [<span data-ttu-id="08115-178">Уменьшитель HTML</span><span class="sxs-lookup"><span data-stu-id="08115-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="08115-179">includeInProject - добавить созданные файлы в файл проекта.</span><span class="sxs-lookup"><span data-stu-id="08115-179">includeInProject - add generated files to project file.</span></span> <span data-ttu-id="08115-180">**Необязательный**, *по умолчанию - false*</span><span class="sxs-lookup"><span data-stu-id="08115-180">**optional**, *default - false*</span></span>
* <span data-ttu-id="08115-181">Сопоставления - формирования сопоставлений источника для объединенный файл.</span><span class="sxs-lookup"><span data-stu-id="08115-181">sourceMaps - generate source maps for the bundled file.</span></span> <span data-ttu-id="08115-182">**Необязательный**, *по умолчанию - false*</span><span class="sxs-lookup"><span data-stu-id="08115-182">**optional**, *default - false*</span></span>

### <a name="visual-studio-2015--2017"></a><span data-ttu-id="08115-183">Visual Studio 2015 / 2017</span><span class="sxs-lookup"><span data-stu-id="08115-183">Visual Studio 2015 / 2017</span></span>

<span data-ttu-id="08115-184">Откройте `bundleconfig.json` в Visual Studio, если среде не имеет расширение установлено; запрос выводится что предполагает отсутствие, может помочь с этим типом файла.</span><span class="sxs-lookup"><span data-stu-id="08115-184">Open `bundleconfig.json` in Visual Studio, if your environment does not have the extension installed; a prompt is presented suggesting that there is one that could assist with this file type.</span></span>

![Расширение BuildBundlerMinifier предложений](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

<span data-ttu-id="08115-186">Выберите представление расширений и установите **Bundler & Уменьшитель** расширения (Visual Studio, требуется перезагрузка).</span><span class="sxs-lookup"><span data-stu-id="08115-186">Select View Extensions, and install the **Bundler & Minifier** extension (Requires Visual Studio restart).</span></span>

![Расширение BuildBundlerMinifier предложений](../client-side/bundling-and-minification/_static/view-extension.png)

<span data-ttu-id="08115-188">После завершения перезагрузки необходимо настроить сборку для запуска процессов минификации и объединения клиентских средств.</span><span class="sxs-lookup"><span data-stu-id="08115-188">When the restart is complete, you need to configure the build to run the processes of minifying and bundling the client-side assets.</span></span> <span data-ttu-id="08115-189">Щелкните правой кнопкой мыши `bundleconfig.json` файла и выберите *Enable пакета на сборки... *.</span><span class="sxs-lookup"><span data-stu-id="08115-189">Right-click the `bundleconfig.json` file and select *Enable bundle on build...*.</span></span>

<span data-ttu-id="08115-190">Выполните построение проекта и `bundleconfig.json` включается в процесс построения для создания выходных файлов, на основе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="08115-190">Build the project, and the `bundleconfig.json` is included in the build process to produce the output files based on the configuration.</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a><span data-ttu-id="08115-191">Код Visual Studio или командной строки</span><span class="sxs-lookup"><span data-stu-id="08115-191">Visual Studio Code or Command Line</span></span>

<span data-ttu-id="08115-192">Visual Studio и расширение управления процессом объединение и Минификация, с помощью жестов графического пользовательского интерфейса; Однако те же возможности доступны с `dotnet` CLI и BuildBundlerMinifier NuGet пакет.</span><span class="sxs-lookup"><span data-stu-id="08115-192">Visual Studio and the extension drive the bundling and minification process using GUI gestures; however, the same capabilities are available with the `dotnet` CLI and BuildBundlerMinifier NuGet package.</span></span>

<span data-ttu-id="08115-193">Добавьте пакет NuGet в проект.</span><span class="sxs-lookup"><span data-stu-id="08115-193">Add the NuGet package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="08115-194">Восстановление зависимости.</span><span class="sxs-lookup"><span data-stu-id="08115-194">Restore the dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="08115-195">Построение приложения:</span><span class="sxs-lookup"><span data-stu-id="08115-195">Build the app:</span></span>

```console
dotnet build
```

<span data-ttu-id="08115-196">Выходные данные команды построения показаны результаты минификации и/или объединении в соответствии с настроенным.</span><span class="sxs-lookup"><span data-stu-id="08115-196">The output from the build command shows the results of the minification and/or bundling according to what is configured.</span></span>

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a><span data-ttu-id="08115-197">Добавление файлов</span><span class="sxs-lookup"><span data-stu-id="08115-197">Adding files</span></span>

<span data-ttu-id="08115-198">В этом примере добавляется вызываемой дополнительный файл CSS `custom.css` и настроен для объединение и Минификация с `site.css`, полученный в одном `site.min.css`.</span><span class="sxs-lookup"><span data-stu-id="08115-198">In this example, an additional CSS file is added called `custom.css` and configured for bundling and minification with `site.css`, resulting in a single `site.min.css`.</span></span>

<span data-ttu-id="08115-199">Custom.CSS</span><span class="sxs-lookup"><span data-stu-id="08115-199">custom.css</span></span>

```css
.about, [role=main], [role=complementary]
{
    margin-top: 60px;
}

footer
{
    margin-top: 10px;
}
```

<span data-ttu-id="08115-200">Добавьте относительный путь к `bundleconfig.json`.</span><span class="sxs-lookup"><span data-stu-id="08115-200">Add the relative path to `bundleconfig.json`.</span></span>

<span data-ttu-id="08115-201">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]</span><span class="sxs-lookup"><span data-stu-id="08115-201">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]</span></span>

> [!NOTE]
> <span data-ttu-id="08115-202">Кроме того, можно использовать шаблон глобализацию - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` получающий CSS все файлы и исключает шаблон уменьшенный файла.</span><span class="sxs-lookup"><span data-stu-id="08115-202">Alternatively, the globbing pattern could be used - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` which gets all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="08115-203">Построение приложения и при открытии `site.min.css`, вы теперь Обратите внимание, что содержимое `custom.css` добавляются в конец файла.</span><span class="sxs-lookup"><span data-stu-id="08115-203">Build the application and if you open `site.min.css`, you'll now notice that contents of `custom.css` has been appended to the end of the file.</span></span>

## <a name="controlling-bundling-and-minification"></a><span data-ttu-id="08115-204">Управление объединение и Минификация</span><span class="sxs-lookup"><span data-stu-id="08115-204">Controlling bundling and minification</span></span>

<span data-ttu-id="08115-205">Как правило вы хотите использовать пакетные и уменьшенный файлы приложения только в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="08115-205">In general, you want to use the bundled and minified files of your app only in a production environment.</span></span> <span data-ttu-id="08115-206">Во время разработки вы хотите использовать исходные файлы, чтобы упростить процесс отладки приложения.</span><span class="sxs-lookup"><span data-stu-id="08115-206">During development, you want to use your original files so your app is easier to debug.</span></span>

<span data-ttu-id="08115-207">Можно указать, какие сценарии и файлы CSS для включения в использование вспомогательного метода тега среды на страницах макета страниц (в разделе [вспомогательных функций тегов](../mvc/views/tag-helpers/index.md)).</span><span class="sxs-lookup"><span data-stu-id="08115-207">You can specify which scripts and CSS files to include in your pages using the environment tag helper in your layout pages (see [Tag Helpers](../mvc/views/tag-helpers/index.md)).</span></span> <span data-ttu-id="08115-208">При запуске в определенных средах вспомогательный тега среды будет отображать только его содержимое.</span><span class="sxs-lookup"><span data-stu-id="08115-208">The environment tag helper will only render its contents when running in specific environments.</span></span> <span data-ttu-id="08115-209">В разделе [работа с несколькими средами](../fundamentals/environments.md) подробные сведения об указании текущей среды.</span><span class="sxs-lookup"><span data-stu-id="08115-209">See [Working with Multiple Environments](../fundamentals/environments.md) for details on specifying the current environment.</span></span>

<span data-ttu-id="08115-210">Следующие теги среды будет отображаться необработанных файлов CSS, при работе в `Development` среде:</span><span class="sxs-lookup"><span data-stu-id="08115-210">The following environment tag will render the unprocessed CSS files when running in the `Development` environment:</span></span>

<span data-ttu-id="08115-211">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]</span><span class="sxs-lookup"><span data-stu-id="08115-211">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]</span></span>

<span data-ttu-id="08115-212">Этот тег среды будет отображаться распространение и уменьшенный CSS-файл только при работе в `Production` или `Staging`:</span><span class="sxs-lookup"><span data-stu-id="08115-212">This environment tag will render the bundled and minified CSS files only when running in `Production` or `Staging`:</span></span>

<span data-ttu-id="08115-213">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]</span><span class="sxs-lookup"><span data-stu-id="08115-213">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]</span></span>

## <a name="consuming-bundleconfigjson-from-gulp"></a><span data-ttu-id="08115-214">Использование bundleconfig.json из Gulp</span><span class="sxs-lookup"><span data-stu-id="08115-214">Consuming bundleconfig.json from Gulp</span></span>

<span data-ttu-id="08115-215">Если рабочий процесс приложения объединение и Минификация требуются дополнительные действия, такие как обработка изображений, busting кэша, обработки assest CDN, т. д., можно преобразовать в процессе пакета и Minify в Gulp.</span><span class="sxs-lookup"><span data-stu-id="08115-215">If your app bundling and minification workflow requires additional processes such as image processing, cache busting, CDN assest processing, etc., then you can convert the Bundle and Minify process to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="08115-216">Преобразование параметр доступен только в Visual Studio 2015 и 2017 г.</span><span class="sxs-lookup"><span data-stu-id="08115-216">Conversion option only available in Visual Studio 2015 and 2017.</span></span>

<span data-ttu-id="08115-217">Щелкните правой кнопкой мыши `bundleconfig.json` и выберите **преобразовать Gulp... **. Это создаст `gulpfile.js` и установите пакеты необходимых npm.</span><span class="sxs-lookup"><span data-stu-id="08115-217">Right-click the `bundleconfig.json` and select **Convert to Gulp...**. This will generate the `gulpfile.js` and install the necessary npm packages.</span></span>

![Преобразовать в Gulp](../client-side/bundling-and-minification/_static/convert-togulp.png)

<span data-ttu-id="08115-219">`gulpfile.js` Полученных операций чтения `bundleconfig.json` файла конфигурации, поэтому он может продолжать использоваться для ввода вывода и параметры.</span><span class="sxs-lookup"><span data-stu-id="08115-219">The `gulpfile.js` produced reads the `bundleconfig.json` file for the configuration, therefore it can continue to be used for the inputs/outputs and settings.</span></span>

<span data-ttu-id="08115-220">[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]</span><span class="sxs-lookup"><span data-stu-id="08115-220">[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]</span></span>

<span data-ttu-id="08115-221">Чтобы включить Gulp при построении проекта в Visual Studio 2017 г, добавьте следующий файл *.csproj:</span><span class="sxs-lookup"><span data-stu-id="08115-221">To enable Gulp when the project builds in Visual Studio 2017, add the following to the *.csproj file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

<span data-ttu-id="08115-222">Чтобы включить Gulp при построении проекта в Visual Studio 2015, добавьте следующий код в `project.json` файла:</span><span class="sxs-lookup"><span data-stu-id="08115-222">To enable Gulp when the project builds in Visual Studio 2015, add the following to the `project.json` file:</span></span>

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a><span data-ttu-id="08115-223">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="08115-223">Additional resources</span></span>

* [<span data-ttu-id="08115-224">Использование Gulp</span><span class="sxs-lookup"><span data-stu-id="08115-224">Using Gulp</span></span>](using-gulp.md)
* [<span data-ttu-id="08115-225">С помощью Grunt</span><span class="sxs-lookup"><span data-stu-id="08115-225">Using Grunt</span></span>](using-grunt.md)
* [<span data-ttu-id="08115-226">Работа с несколькими средами</span><span class="sxs-lookup"><span data-stu-id="08115-226">Working with Multiple Environments</span></span>](../fundamentals/environments.md)
* [<span data-ttu-id="08115-227">Вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="08115-227">Tag Helpers</span></span>](../mvc/views/tag-helpers/index.md)