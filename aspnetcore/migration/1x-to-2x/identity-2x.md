---
title: "Миграция проверки подлинности и удостоверение ядру ASP.NET 2.0"
author: scottaddie
description: "В этой статье описаны наиболее распространенные действия для переноса 1.x ASP.NET Core проверку подлинности и удостоверение по Core ASP.NET 2.0."
keywords: "Проверка подлинности ASP.NET Core, удостоверение,"
ms.author: scaddie
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: b4e67e7cfea3c01e3ca8c0d5df2a04e789749932
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2017
---
# <a name="migrating-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="55352-104">Миграция проверку подлинности и удостоверение для основных компонентов ASP.NET 2.0</span><span class="sxs-lookup"><span data-stu-id="55352-104">Migrating Authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="55352-105">По [Scott Addie](https://github.com/scottaddie) и [поздравить Hao](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="55352-105">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="55352-106">Ядро ASP.NET 2.0 имеет новую модель для проверки подлинности и [удостоверение](xref:security/authentication/identity) с помощью служб, который упрощает настройку.</span><span class="sxs-lookup"><span data-stu-id="55352-106">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="55352-107">Приложения ASP.NET Core 1.x, использующие проверку подлинности или идентификаторов можно обновить для использования новой модели, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="55352-107">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="55352-108">По промежуточного слоя проверки подлинности и службы</span><span class="sxs-lookup"><span data-stu-id="55352-108">Authentication Middleware and Services</span></span>
<span data-ttu-id="55352-109">В проектах 1.x настроена проверка подлинности через по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="55352-109">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="55352-110">Для каждой схемы проверки подлинности, которые требуется поддерживать вызывается метод по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="55352-110">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="55352-111">В следующем примере 1.x настраивает проверку подлинности Facebook с удостоверения *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-111">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions { 
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
} 
```

<span data-ttu-id="55352-112">В проектах на 2.0 через службы настроена проверка подлинности.</span><span class="sxs-lookup"><span data-stu-id="55352-112">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="55352-113">Каждая схема проверки подлинности, зарегистрированный в `ConfigureServices` метод *файла Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="55352-113">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="55352-114">`UseIdentity` Заменяется метод `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="55352-114">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="55352-115">Пример 2.0 настройки проверки подлинности Facebook с удостоверения *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-115">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="55352-116">`UseAuthentication` Метод добавляет компонент по промежуточного слоя одной проверки подлинности, который отвечает за автоматической проверки подлинности и обработки запросов на удаленную проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="55352-116">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="55352-117">Он заменяет все компоненты отдельных по промежуточного слоя единого компонент по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="55352-117">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="55352-118">Ниже приведены 2.0 инструкции по миграции для каждой схемы основной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="55352-118">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="55352-119">Проверка подлинности на основе файлов cookie</span><span class="sxs-lookup"><span data-stu-id="55352-119">Cookie-based Authentication</span></span>
<span data-ttu-id="55352-120">Выберите один из приведенных ниже параметров и внесите необходимые изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-120">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="55352-121">Использование файлов cookie с удостоверением</span><span class="sxs-lookup"><span data-stu-id="55352-121">Use cookies with Identity</span></span>
    - <span data-ttu-id="55352-122">Замените `UseIdentity` с `UseAuthentication` в `Configure` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-122">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="55352-123">Вызвать `AddIdentity` метод в `ConfigureServices` метод, чтобы добавить службы проверки подлинности файла cookie.</span><span class="sxs-lookup"><span data-stu-id="55352-123">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="55352-124">При необходимости вызывать `ConfigureApplicationCookie` или `ConfigureExternalCookie` метод в `ConfigureServices` метод, чтобы настроить параметры файлов cookie удостоверений.</span><span class="sxs-lookup"><span data-stu-id="55352-124">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="55352-125">Использование файлов cookie без идентификатора</span><span class="sxs-lookup"><span data-stu-id="55352-125">Use cookies without Identity</span></span>
    - <span data-ttu-id="55352-126">Замените `UseCookieAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-126">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - <span data-ttu-id="55352-127">Вызвать `AddAuthentication` и `AddCookie` методы в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-127">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User, 
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options => {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="55352-128">Проверки подлинности носителя JWT</span><span class="sxs-lookup"><span data-stu-id="55352-128">JWT Bearer Authentication</span></span>
<span data-ttu-id="55352-129">Внесите следующие изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-129">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="55352-130">Замените `UseJwtBearerAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-130">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="55352-131">Вызвать `AddJwtBearer` метод в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-131">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="55352-132">Этот фрагмент кода не использует удостоверение, чтобы задать схему по умолчанию, передав `JwtBearerDefaults.AuthenticationScheme` для `AddAuthentication` метод.</span><span class="sxs-lookup"><span data-stu-id="55352-132">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="55352-133">OpenID Connect проверки подлинности (OIDC)</span><span class="sxs-lookup"><span data-stu-id="55352-133">OpenID Connect (OIDC) Authentication</span></span>
<span data-ttu-id="55352-134">Внесите следующие изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-134">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="55352-135">Замените `UseOpenIdConnectAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-135">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="55352-136">Вызвать `AddOpenIdConnect` метод в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-136">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options => {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options => {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

### <a name="facebook-authentication"></a><span data-ttu-id="55352-137">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="55352-137">Facebook Authentication</span></span>
<span data-ttu-id="55352-138">Внесите следующие изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-138">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="55352-139">Замените `UseFacebookAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-139">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="55352-140">Вызвать `AddFacebook` метод в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-140">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="55352-141">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="55352-141">Google Authentication</span></span>
<span data-ttu-id="55352-142">Внесите следующие изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-142">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="55352-143">Замените `UseGoogleAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-143">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="55352-144">Вызвать `AddGoogle` метод в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-144">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="55352-145">Проверка подлинности учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="55352-145">Microsoft Account Authentication</span></span>
<span data-ttu-id="55352-146">Внесите следующие изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-146">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="55352-147">Замените `UseMicrosoftAccountAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-147">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="55352-148">Вызвать `AddMicrosoftAccount` метод в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-148">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a><span data-ttu-id="55352-149">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="55352-149">Twitter Authentication</span></span>
<span data-ttu-id="55352-150">Внесите следующие изменения в *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-150">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="55352-151">Замените `UseTwitterAuthentication` вызов метода `Configure` метод с `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-151">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="55352-152">Вызвать `AddTwitter` метод в `ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-152">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="55352-153">Параметр схемы проверки подлинности по умолчанию</span><span class="sxs-lookup"><span data-stu-id="55352-153">Setting Default Authentication Schemes</span></span>
<span data-ttu-id="55352-154">В 1.x `AutomaticAuthenticate` и `AutomaticChallenge` свойства должны устанавливаться на одна схема проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="55352-154">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="55352-155">Произошла хороший способ контроля.</span><span class="sxs-lookup"><span data-stu-id="55352-155">There was no good way to enforce this.</span></span>

<span data-ttu-id="55352-156">В версии 2.0, эти свойства будут удалены как флаги в отдельных `AuthenticationOptions` экземпляра и были перемещены в базу [AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) класса.</span><span class="sxs-lookup"><span data-stu-id="55352-156">In 2.0, these two properties have been removed as flags on the individual `AuthenticationOptions` instance and have moved into the base [AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) class.</span></span> <span data-ttu-id="55352-157">Свойства можно настроить в `AddAuthentication` вызова метода внутри `ConfigureServices` метод *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-157">The properties can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="55352-158">Кроме того, используйте перегруженную версию `AddAuthentication` метод, чтобы задать более одного свойства.</span><span class="sxs-lookup"><span data-stu-id="55352-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="55352-159">В следующем примере перегруженный метод схемы по умолчанию выбрана `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="55352-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="55352-160">Схема проверки подлинности можно также может быть указан в пределах индивидуальными `[Authorize]` атрибутов или политики авторизации.</span><span class="sxs-lookup"><span data-stu-id="55352-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options => {
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="55352-161">Определите схема по умолчанию в версии 2.0, если выполняется одно из следующих условий:</span><span class="sxs-lookup"><span data-stu-id="55352-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="55352-162">Пользователю необходимо автоматически войти в</span><span class="sxs-lookup"><span data-stu-id="55352-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="55352-163">Вы используете `[Authorize]` атрибута или авторизации политики без указания схемы</span><span class="sxs-lookup"><span data-stu-id="55352-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="55352-164">Исключением из этого правила является `AddIdentity` метод.</span><span class="sxs-lookup"><span data-stu-id="55352-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="55352-165">Этот метод добавляет файлы cookie для вас и задает значение по умолчанию проверки подлинности и проверку схемы в файл cookie приложения `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="55352-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="55352-166">Кроме того, он задает схему по умолчанию вход внешний файл cookie `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="55352-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="55352-167">Используйте HttpContext модули проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="55352-167">Use HttpContext Authentication Extensions</span></span>
<span data-ttu-id="55352-168">`IAuthenticationManager` Интерфейс — Главная точка входа в систему проверки подлинности 1.x.</span><span class="sxs-lookup"><span data-stu-id="55352-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="55352-169">Он был заменен с новым набором `HttpContext` методы расширения в `Microsoft.AspNetCore.Authentication` пространства имен.</span><span class="sxs-lookup"><span data-stu-id="55352-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="55352-170">Например, 1.x проекты ссылки `Authentication` свойство:</span><span class="sxs-lookup"><span data-stu-id="55352-170">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="55352-171">В проектах на 2.0 импортируйте `Microsoft.AspNetCore.Authentication` пространства имен и удалите `Authentication` ссылок на свойства:</span><span class="sxs-lookup"><span data-stu-id="55352-171">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="55352-172">Проверка подлинности Windows (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="55352-172">Windows Authentication (HTTP.sys / IISIntegration)</span></span>
<span data-ttu-id="55352-173">Существует два типа проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="55352-173">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="55352-174">Узел позволяет только прошедшие проверку</span><span class="sxs-lookup"><span data-stu-id="55352-174">The host only allows authenticated users</span></span>
2. <span data-ttu-id="55352-175">Узел позволяет как анонимные, а проверка подлинности пользователей</span><span class="sxs-lookup"><span data-stu-id="55352-175">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="55352-176">Первый вариант, описанных выше 2.0 изменения не влияют.</span><span class="sxs-lookup"><span data-stu-id="55352-176">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="55352-177">Второй вариант, описанных выше влияют изменения 2.0.</span><span class="sxs-lookup"><span data-stu-id="55352-177">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="55352-178">Например, вам может разрешение анонимных пользователей в приложение на IIS или [HTTP.sys](xref:fundamentals/servers/weblistener) слоя но авторизацию пользователей на уровне контроллера.</span><span class="sxs-lookup"><span data-stu-id="55352-178">As an example, you may be allowing anonymous users into your application at the IIS or [HTTP.sys](xref:fundamentals/servers/weblistener) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="55352-179">В этом случае значение схемы по умолчанию `IISDefaults.AuthenticationScheme` в `ConfigureServices` метод *файла Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-179">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="55352-180">Не удалось задать схему по умолчанию соответствующим образом предотвращает запроса authorize проверить работу.</span><span class="sxs-lookup"><span data-stu-id="55352-180">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="55352-181">Экземпляры IdentityCookieOptions</span><span class="sxs-lookup"><span data-stu-id="55352-181">IdentityCookieOptions Instances</span></span>
<span data-ttu-id="55352-182">Побочный эффект изменений 2.0 является параметром для использование именованных параметров вместо экземпляров параметры файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="55352-182">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="55352-183">Позволяет настраивать имена схем идентификатора файла cookie удаляется.</span><span class="sxs-lookup"><span data-stu-id="55352-183">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="55352-184">Например, 1.x проекты использующие [внедрение конструктора](xref:mvc/controllers/dependency-injection#constructor-injection) для передачи `IdentityCookieOptions` параметр в *AccountController.cs*.</span><span class="sxs-lookup"><span data-stu-id="55352-184">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="55352-185">Схему проверки подлинности внешних файлов cookie осуществляется из предоставленного экземпляра.</span><span class="sxs-lookup"><span data-stu-id="55352-185">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="55352-186">Внедрение упомянутой выше конструктора необязательной 2.0 проектах и `_externalCookieScheme` удаление поля:</span><span class="sxs-lookup"><span data-stu-id="55352-186">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="55352-187">`IdentityConstants.ExternalScheme` Константа может быть использована напрямую:</span><span class="sxs-lookup"><span data-stu-id="55352-187">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="55352-188">Добавление свойства навигации IdentityUser POCO</span><span class="sxs-lookup"><span data-stu-id="55352-188">Add IdentityUser POCO Navigation Properties</span></span>
<span data-ttu-id="55352-189">Entity Framework (EF) основные свойства навигации базового `IdentityUser` POCO (Plain объект среды CLR) были удалены.</span><span class="sxs-lookup"><span data-stu-id="55352-189">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="55352-190">При использовании этих свойств проекта 1.x вручную добавьте их к проекту для версии 2.0:</span><span class="sxs-lookup"><span data-stu-id="55352-190">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="55352-191">Для предотвращения повторяющихся внешних ключей при выполнении миграции Core EF, добавьте следующий код в вашей `IdentityDbContext` класса `OnModelCreating` метод (после `base.OnModelCreating();` вызова):</span><span class="sxs-lookup"><span data-stu-id="55352-191">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="55352-192">Замените GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="55352-192">Replace GetExternalAuthenticationSchemes</span></span>
<span data-ttu-id="55352-193">Синхронный метод `GetExternalAuthenticationSchemes` был удален в пользу асинхронную версию.</span><span class="sxs-lookup"><span data-stu-id="55352-193">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="55352-194">проекты 1.x имеется следующий код *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="55352-194">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="55352-195">Этот метод представлен в *Login.cshtml* слишком:</span><span class="sxs-lookup"><span data-stu-id="55352-195">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="55352-196">В проектах 2.0, используйте `GetExternalAuthenticationSchemesAsync` метод:</span><span class="sxs-lookup"><span data-stu-id="55352-196">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="55352-197">В *Login.cshtml*, `AuthenticationScheme` свойство с доступом в `foreach` примет цикла `Name`:</span><span class="sxs-lookup"><span data-stu-id="55352-197">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="55352-198">Изменение свойства ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="55352-198">ManageLoginsViewModel Property Change</span></span>
<span data-ttu-id="55352-199">Объект `ManageLoginsViewModel` объект используется в `ManageLogins` действие *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="55352-199">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="55352-200">1.x проектах объект элемента `OtherLogins` свойство возвращаемого типа `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="55352-200">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="55352-201">Этот тип возвращаемого значения требуется импорте `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="55352-201">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="55352-202">В проектах 2.0, возвращаемый тип изменения в `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="55352-202">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="55352-203">Этот новый тип возврата требуется замена `Microsoft.AspNetCore.Http.Authentication` импорта с `Microsoft.AspNetCore.Authentication` импорта.</span><span class="sxs-lookup"><span data-stu-id="55352-203">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="55352-204">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="55352-204">Additional Resources</span></span>
<span data-ttu-id="55352-205">Дополнительные сведения и описание см. в разделе [обсуждение для проверки подлинности 2.0](https://github.com/aspnet/Security/issues/1338) проблемы на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="55352-205">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>