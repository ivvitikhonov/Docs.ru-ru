---
title: "Разделяемые представления"
author: ardalis
description: "С помощью частичного представления в ASP.NET Core MVC"
keywords: "Partial ASP.NET Core, разделяемые представления"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 4be1b12c-b74e-44ff-826b-99ce86e8d464
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/partial
ms.openlocfilehash: 777c4d663f646f3bc3fbe6da0b537d651a1fb4d8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2017
---
# <a name="partial-views"></a><span data-ttu-id="33580-104">Разделяемые представления</span><span class="sxs-lookup"><span data-stu-id="33580-104">Partial Views</span></span>

<span data-ttu-id="33580-105">По [Стив Смит](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), и [Рик Андерсон](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33580-105">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33580-106">ASP.NET Core MVC поддерживает частичного представления, которые могут быть полезны при наличии допускающих многократное использование частей веб-страниц, которые вы хотите поделиться между различными представлениями.</span><span class="sxs-lookup"><span data-stu-id="33580-106">ASP.NET Core MVC supports partial views, which are useful when you have reusable parts of web pages you want to share between different views.</span></span>

[<span data-ttu-id="33580-107">Просмотреть или скачать образец кода</span><span class="sxs-lookup"><span data-stu-id="33580-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample)

## <a name="what-are-partial-views"></a><span data-ttu-id="33580-108">Что такое частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-108">What are Partial Views?</span></span>

<span data-ttu-id="33580-109">Частичное представление — это представление, отображаемого в другое представление.</span><span class="sxs-lookup"><span data-stu-id="33580-109">A partial view is a view that is rendered within another view.</span></span> <span data-ttu-id="33580-110">Выходные данные HTML, созданы при выполнении частичного представления преобразуется в представление вызова (или родительской).</span><span class="sxs-lookup"><span data-stu-id="33580-110">The HTML output generated by executing the partial view is rendered into the calling (or parent) view.</span></span> <span data-ttu-id="33580-111">Как и представления, разделяемые представления использовать *.cshtml* расширение имени файла.</span><span class="sxs-lookup"><span data-stu-id="33580-111">Like views, partial views use the *.cshtml* file extension.</span></span>

## <a name="when-should-i-use-partial-views"></a><span data-ttu-id="33580-112">Когда следует использовать частичные представления</span><span class="sxs-lookup"><span data-stu-id="33580-112">When Should I Use Partial Views?</span></span>

<span data-ttu-id="33580-113">Частичные представления — это эффективный способ разбиение большие представления на более простые компоненты.</span><span class="sxs-lookup"><span data-stu-id="33580-113">Partial views are an effective way of breaking up large views into smaller components.</span></span> <span data-ttu-id="33580-114">Они, можно сократить дублирование представления содержимого и просмотр элементов для повторного использования.</span><span class="sxs-lookup"><span data-stu-id="33580-114">They can reduce duplication of view content and allow view elements to be reused.</span></span> <span data-ttu-id="33580-115">Общие элементы макета должно быть указано в [_Layout.cshtml](layout.md).</span><span class="sxs-lookup"><span data-stu-id="33580-115">Common layout elements should be specified in [_Layout.cshtml](layout.md).</span></span> <span data-ttu-id="33580-116">Содержимое для повторного использования не макета можно инкапсулировать в частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-116">Non-layout reusable content can be encapsulated into partial views.</span></span>

<span data-ttu-id="33580-117">При наличии сложных страницы состоит из нескольких логических частей может быть полезно для работы с каждый фрагмент как свой собственный частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-117">If you have a complex page made up of several logical pieces, it can be helpful to work with each piece as its own partial view.</span></span> <span data-ttu-id="33580-118">Каждой части страницы можно просматривать отдельно от остальной части страницы и представления для самой страницы становится гораздо проще, так как он содержит только общую структуру страницы и вызовы для отображения частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-118">Each piece of the page can be viewed in isolation from the rest of the page, and the view for the page itself becomes much simpler since it only contains the overall page structure and calls to render the partial views.</span></span>

<span data-ttu-id="33580-119">Совет: Выполните [не повторять самостоятельно принцип](http://deviq.com/don-t-repeat-yourself/) в представления.</span><span class="sxs-lookup"><span data-stu-id="33580-119">Tip: Follow the [Don't Repeat Yourself Principle](http://deviq.com/don-t-repeat-yourself/) in your views.</span></span>

## <a name="declaring-partial-views"></a><span data-ttu-id="33580-120">Объявление частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-120">Declaring Partial Views</span></span>

<span data-ttu-id="33580-121">Разделяемые представления создаются как любое другое представление: вы создаете *.cshtml* файл включен в *представления* папки.</span><span class="sxs-lookup"><span data-stu-id="33580-121">Partial views are created like any other view: you create a *.cshtml* file within the *Views* folder.</span></span> <span data-ttu-id="33580-122">Нет семантические различия между представлением и обычное представление — они просто отображаются по-разному.</span><span class="sxs-lookup"><span data-stu-id="33580-122">There is no semantic difference between a partial view and a regular view - they are just rendered differently.</span></span> <span data-ttu-id="33580-123">У вас есть представление, которое возвращается непосредственно из контроллера `ViewResult`, и тем же самым представлением можно использовать в качестве частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-123">You can have a view that is returned directly from a controller's `ViewResult`, and the same view can be used as a partial view.</span></span> <span data-ttu-id="33580-124">— Основное различие между просмотру представление и частичного представления, разделяемые представления не выполнен *файл _ViewStart.cshtml* (хотя представления Дополнительные сведения о *файл _ViewStart.cshtml* в [макета ](layout.md)).</span><span class="sxs-lookup"><span data-stu-id="33580-124">The main difference between how a view and a partial view are rendered is that partial views do not run *_ViewStart.cshtml* (while views do - learn more about *_ViewStart.cshtml* in [Layout](layout.md)).</span></span>

## <a name="referencing-a-partial-view"></a><span data-ttu-id="33580-125">Ссылка на частичное представление</span><span class="sxs-lookup"><span data-stu-id="33580-125">Referencing a Partial View</span></span>

<span data-ttu-id="33580-126">Из в страницу представления, существует несколько способов, в которых можно вывести частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-126">From within a view page, there are several ways in which you can render a partial view.</span></span> <span data-ttu-id="33580-127">Проще всего использовать `Html.Partial`, который возвращает `IHtmlString` и можно указывать с помощью префикса вызова с `@`:</span><span class="sxs-lookup"><span data-stu-id="33580-127">The simplest is to use `Html.Partial`, which returns an `IHtmlString` and can be referenced by prefixing the call with `@`:</span></span>

<span data-ttu-id="33580-128">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=9)]</span><span class="sxs-lookup"><span data-stu-id="33580-128">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=9)]</span></span>

<span data-ttu-id="33580-129">`PartialAsync` Метод доступен для частичного представления содержащего асинхронного кода (несмотря на то, что код в представлениях, обычно не рекомендуется):</span><span class="sxs-lookup"><span data-stu-id="33580-129">The `PartialAsync` method is available for partial views containing asynchronous code (although code in views is generally discouraged):</span></span>

<span data-ttu-id="33580-130">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=8)]</span><span class="sxs-lookup"><span data-stu-id="33580-130">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=8)]</span></span>

<span data-ttu-id="33580-131">Можно вывести частичного представления с `RenderPartial`.</span><span class="sxs-lookup"><span data-stu-id="33580-131">You can render a partial view with `RenderPartial`.</span></span> <span data-ttu-id="33580-132">Этот метод не возвращает результат. выполняет потоковую передачу выводимые данные непосредственно в ответе.</span><span class="sxs-lookup"><span data-stu-id="33580-132">This method doesn't return a result; it streams the rendered output directly to the response.</span></span> <span data-ttu-id="33580-133">Так как он не возвращает результат, он должен быть вызван в блоке кода Razor (можно также вызвать `RenderPartialAsync` при необходимости):</span><span class="sxs-lookup"><span data-stu-id="33580-133">Because it doesn't return a result, it must be called within a Razor code block (you can also call `RenderPartialAsync` if necessary):</span></span>

<span data-ttu-id="33580-134">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=10-12)]</span><span class="sxs-lookup"><span data-stu-id="33580-134">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=10-12)]</span></span>

<span data-ttu-id="33580-135">Поскольку потоки результат напрямую, `RenderPartial` и `RenderPartialAsync` работают быстрее, в некоторых сценариях.</span><span class="sxs-lookup"><span data-stu-id="33580-135">Because it streams the result directly, `RenderPartial` and `RenderPartialAsync` may perform better in some scenarios.</span></span> <span data-ttu-id="33580-136">Однако в большинстве случаев рекомендуется можно использовать `Partial` и `PartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="33580-136">However, in most cases it's recommended you use `Partial` and `PartialAsync`.</span></span>

> [!NOTE]
> <span data-ttu-id="33580-137">Если вашим представлениям требуется для выполнения кода, рекомендуется использовать [компонент представления](view-components.md) вместо частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-137">If your views need to execute code, the recommended pattern is to use a [view component](view-components.md) instead of a partial view.</span></span>

### <a name="partial-view-discovery"></a><span data-ttu-id="33580-138">Частичное представление обнаружения</span><span class="sxs-lookup"><span data-stu-id="33580-138">Partial View Discovery</span></span>

<span data-ttu-id="33580-139">При задании частичного представления, можно ссылаться в расположение несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="33580-139">When referencing a partial view, you can refer to its location in several ways:</span></span>

```text
// Uses a view in current folder with this name
// If none is found, searches the Shared folder
@Html.Partial("ViewName")

// A view with this name must be in the same folder
@Html.Partial("ViewName.cshtml")

// Locate the view based on the application root
// Paths that start with "/" or "~/" refer to the application root
@Html.Partial("~/Views/Folder/ViewName.cshtml")
@Html.Partial("/Views/Folder/ViewName.cshtml")

// Locate the view using relative paths
@Html.Partial("../Account/LoginPartial.cshtml")
```

<span data-ttu-id="33580-140">В папках другое представление, могут иметь различные частичного представления, с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="33580-140">You can have different partial views with the same name in different view folders.</span></span> <span data-ttu-id="33580-141">При обращении к представлениям по имени (без расширения файла), представления, в каждой папке используют частичного представления в той же папке, с ними.</span><span class="sxs-lookup"><span data-stu-id="33580-141">When referencing the views by name (without file extension), views in each folder will use the partial view in the same folder with them.</span></span> <span data-ttu-id="33580-142">Можно также указать частичного представления по умолчанию для использования, поместив его в *Shared* папки.</span><span class="sxs-lookup"><span data-stu-id="33580-142">You can also specify a default partial view to use, placing it in the *Shared* folder.</span></span> <span data-ttu-id="33580-143">Общий частичного представления будет использоваться любые представления не имеют собственную версию частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-143">The shared partial view will be used by any views that don't have their own version of the partial view.</span></span> <span data-ttu-id="33580-144">У вас есть представлением по умолчанию (в *Shared*), которой заменяется представлением с тем же именем в той же папке, как родительское представление.</span><span class="sxs-lookup"><span data-stu-id="33580-144">You can have a default partial view (in *Shared*), which is overridden by a partial view with the same name in the same folder as the parent view.</span></span>

<span data-ttu-id="33580-145">Частичные представления могут быть *связанные*.</span><span class="sxs-lookup"><span data-stu-id="33580-145">Partial views can be *chained*.</span></span> <span data-ttu-id="33580-146">То есть частичного представления может вызывать другое разделяемое представление (пока не формируют цикл).</span><span class="sxs-lookup"><span data-stu-id="33580-146">That is, a partial view can call another partial view (as long as you don't create a loop).</span></span> <span data-ttu-id="33580-147">В каждом представлении или частичного представления относительные пути задаются всегда относительно этого представления, не корневой или родительский представления.</span><span class="sxs-lookup"><span data-stu-id="33580-147">Within each view or partial view, relative paths are always relative to that view, not the root or parent view.</span></span>

> [!NOTE]
> <span data-ttu-id="33580-148">При объявлении [Razor](razor.md) `section` в частичного представления, он не будет видимым для его родительских элементов; он будет ограничена частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-148">If you declare a [Razor](razor.md) `section` in a partial view, it will not be visible to its parent(s); it will be limited to the partial view.</span></span>

## <a name="accessing-data-from-partial-views"></a><span data-ttu-id="33580-149">Доступ к данным из частичных представлений</span><span class="sxs-lookup"><span data-stu-id="33580-149">Accessing Data From Partial Views</span></span>

<span data-ttu-id="33580-150">При создании экземпляра частичного представления, он получает копию родительского представления `ViewData` словаря.</span><span class="sxs-lookup"><span data-stu-id="33580-150">When a partial view is instantiated, it gets a copy of the parent view's `ViewData` dictionary.</span></span> <span data-ttu-id="33580-151">Обновления, вносимые в данные в пределах частичного представления, не сохраняются в родительское представление.</span><span class="sxs-lookup"><span data-stu-id="33580-151">Updates made to the data within the partial view are not persisted to the parent view.</span></span> <span data-ttu-id="33580-152">`ViewData`изменения в разделенном представлении теряется при возврате частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-152">`ViewData` changed in a partial view is lost when the partial view returns.</span></span>

<span data-ttu-id="33580-153">Можно передать экземпляр `ViewDataDictionary` для частичного представления:</span><span class="sxs-lookup"><span data-stu-id="33580-153">You can pass an instance of `ViewDataDictionary` to the partial view:</span></span>

```csharp
@Html.Partial("PartialName", customViewData)
   ```

<span data-ttu-id="33580-154">Можно также передать модель в частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-154">You can also pass a model into a partial view.</span></span> <span data-ttu-id="33580-155">Это может быть модели представления страницы, или его часть или пользовательского объекта.</span><span class="sxs-lookup"><span data-stu-id="33580-155">This can be the page's view model, or some portion of it, or a custom object.</span></span> <span data-ttu-id="33580-156">Можно передать модель в `Partial`,`PartialAsync`, `RenderPartial`, или `RenderPartialAsync`:</span><span class="sxs-lookup"><span data-stu-id="33580-156">You can pass a model to `Partial`,`PartialAsync`, `RenderPartial`, or `RenderPartialAsync`:</span></span>

```csharp
@Html.Partial("PartialName", viewModel)
   ```

<span data-ttu-id="33580-157">Можно передать экземпляр `ViewDataDictionary` и модели представления для частичного представления:</span><span class="sxs-lookup"><span data-stu-id="33580-157">You can pass an instance of `ViewDataDictionary` and a view model to a partial view:</span></span>

<span data-ttu-id="33580-158">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml?range=15-16)]</span><span class="sxs-lookup"><span data-stu-id="33580-158">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml?range=15-16)]</span></span>

<span data-ttu-id="33580-159">Ниже показан разметку *Views/Articles/Read.cshtml* представления, содержащего два частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-159">The markup below shows the *Views/Articles/Read.cshtml* view which contains two partial views.</span></span> <span data-ttu-id="33580-160">Передает второй частичного представления в модели и `ViewData` для частичного представления.</span><span class="sxs-lookup"><span data-stu-id="33580-160">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="33580-161">Вы можете передать новые `ViewData` словаря, сохраняя существующий `ViewData` при использовании перегруженный конструктор `ViewDataDictionary` выделением ниже:</span><span class="sxs-lookup"><span data-stu-id="33580-161">You can pass new `ViewData` dictionary while retaining the existing `ViewData` if you use the constructor overload of the `ViewDataDictionary` highlighted below:</span></span>

<span data-ttu-id="33580-162">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="33580-162">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml)]</span></span>

<span data-ttu-id="33580-163">*Представления/общие/AuthorPartial*:</span><span class="sxs-lookup"><span data-stu-id="33580-163">*Views/Shared/AuthorPartial*:</span></span>

<span data-ttu-id="33580-164">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="33580-164">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml)]</span></span>

<span data-ttu-id="33580-165">*ArticleSection* частичного:</span><span class="sxs-lookup"><span data-stu-id="33580-165">The *ArticleSection* partial:</span></span>

<span data-ttu-id="33580-166">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="33580-166">[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml)]</span></span>

<span data-ttu-id="33580-167">Во время выполнения, подготавливаются к просмотру частичными репликами в родительское представление, который в свою очередь подготавливается к просмотру в рамках общего *_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="33580-167">At runtime, the partials are rendered into the parent view, which itself is rendered within the shared *_Layout.cshtml*</span></span>

![Частичное просмотр выходных данных](partial/_static/output.png)