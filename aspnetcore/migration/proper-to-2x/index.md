---
title: "从 ASP.NET 迁移到 ASP.NET Core 2.0"
author: isaac2004
description: "本参考文档提供了从现有的 ASP.NET MVC 或 Web API 应用程序迁移到 ASP.NET Core 2.0 的指南。"
keywords: "ASP.NET Core, MVC, 迁移"
ms.author: scaddie
manager: wpickett
ms.date: 08/27/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/proper-to-2x/index
ms.openlocfilehash: 6e32ee64970bac5d7f09207e570f543477745b60
ms.sourcegitcommit: 8cafdd1dd409d5070d227100ba0e094c779ac47b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2017
---
# <a name="migrating-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="d256b-104">从 ASP.NET 迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="d256b-104">Migrating from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="d256b-105">作者：[Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="d256b-105">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="d256b-106">本文可作为从 ASP.NET 应用程序迁移到 ASP.NET Core 2.0 的参考指南。</span><span class="sxs-lookup"><span data-stu-id="d256b-106">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d256b-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="d256b-107">Prerequisites</span></span>

* <span data-ttu-id="d256b-108">[.NET Core 2.0.0 SDK](https://dot.net/core) 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d256b-108">[.NET Core 2.0.0 SDK](https://dot.net/core) or later.</span></span>
* <span data-ttu-id="d256b-109">已安装“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 版本 15.3 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d256b-109">[Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) version 15.3 or later with the **ASP.NET and web development** workload.</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="d256b-110">目标框架</span><span class="sxs-lookup"><span data-stu-id="d256b-110">Target Frameworks</span></span>
<span data-ttu-id="d256b-111">ASP.NET Core 2.0 项目为开发人员提供了面向 .NET Core、.NET Framework 或同时面向这两者的灵活性。</span><span class="sxs-lookup"><span data-stu-id="d256b-111">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="d256b-112">若要确定最合适的目标框架，请参阅[为服务器应用选择 .NET Core 或 .NET Framework](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)。</span><span class="sxs-lookup"><span data-stu-id="d256b-112">See [Choosing between .NET Core and .NET Framework for server apps](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="d256b-113">面向 .NET Framework 时，项目需要引用单个 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d256b-113">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="d256b-114">得益于有 ASP.NET Core 2.0 [元包](xref:fundamentals/metapackage)，面向 .NET Core 时可以避免进行大量的显式包引用。</span><span class="sxs-lookup"><span data-stu-id="d256b-114">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="d256b-115">在项目中安装 `Microsoft.AspNetCore.All` 元包：</span><span class="sxs-lookup"><span data-stu-id="d256b-115">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
</ItemGroup>
```

<span data-ttu-id="d256b-116">使用此元包时，应用不会部署元包中引用的任何包。</span><span class="sxs-lookup"><span data-stu-id="d256b-116">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="d256b-117">.NET Core 运行时存储中包含这些资产，并且为提高性能已对它们进行预编译。</span><span class="sxs-lookup"><span data-stu-id="d256b-117">The .NET Core Runtime Store includes these assets, and they are precompiled to improve performance.</span></span> <span data-ttu-id="d256b-118">请参阅 [ASP.NET Core 2.x 的 Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="d256b-118">See [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x](xref:fundamentals/metapackage) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="d256b-119">项目结构差异</span><span class="sxs-lookup"><span data-stu-id="d256b-119">Project structure differences</span></span>
<span data-ttu-id="d256b-120">ASP.NET Core 中简化了 .csproj 文件格式。</span><span class="sxs-lookup"><span data-stu-id="d256b-120">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="d256b-121">下面是一些显著的更改：</span><span class="sxs-lookup"><span data-stu-id="d256b-121">Some notable changes include:</span></span>
- <span data-ttu-id="d256b-122">若要将文件作为项目的组成部分，无需显示包含它们。</span><span class="sxs-lookup"><span data-stu-id="d256b-122">Explicit inclusion of files is not necessary for them to be considered part of the project.</span></span> <span data-ttu-id="d256b-123">服务于大型团队时，这可减少出现 XML 合并冲突的风险。</span><span class="sxs-lookup"><span data-stu-id="d256b-123">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="d256b-124">没有对其他项目的基于 GUID 的引用，这可以提高文件的可读性。</span><span class="sxs-lookup"><span data-stu-id="d256b-124">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="d256b-125">无需在 Visual Studio 中卸载文件即可对它进行编辑：</span><span class="sxs-lookup"><span data-stu-id="d256b-125">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Visual Studio 2017 中的“编辑 CSPROJ”上下文菜单选项](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="d256b-127">Global.asax 文件替换</span><span class="sxs-lookup"><span data-stu-id="d256b-127">Global.asax file replacement</span></span>
<span data-ttu-id="d256b-128">ASP.NET Core 引入了启动应用的新机制。</span><span class="sxs-lookup"><span data-stu-id="d256b-128">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="d256b-129">ASP.NET 应用程序的入口点是 Global.asax 文件。</span><span class="sxs-lookup"><span data-stu-id="d256b-129">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="d256b-130">路由配置及筛选器和区域注册等任务在 Global.asax 文件中进行处理。</span><span class="sxs-lookup"><span data-stu-id="d256b-130">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

<span data-ttu-id="d256b-131">[!code-csharp[Main](samples/globalasax-sample.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-131">[!code-csharp[Main](samples/globalasax-sample.cs)]</span></span>

<span data-ttu-id="d256b-132">此方法会将应用程序和应用程序要部署到的服务器耦合在一起，并且它们的耦合方式会干扰实现。</span><span class="sxs-lookup"><span data-stu-id="d256b-132">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="d256b-133">为了将它们分离，引入了 [OWIN](http://owin.org/) 来提供一种更为简便的同时使用多个框架的方法。</span><span class="sxs-lookup"><span data-stu-id="d256b-133">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="d256b-134">OWIN 提供了一个管道，可以只添加所需的模块。</span><span class="sxs-lookup"><span data-stu-id="d256b-134">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="d256b-135">托管环境使用 [Startup](xref:fundamentals/startup) 函数配置服务和应用的请求管道。</span><span class="sxs-lookup"><span data-stu-id="d256b-135">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="d256b-136">`Startup` 在应用程序中注册一组中间件。</span><span class="sxs-lookup"><span data-stu-id="d256b-136">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="d256b-137">对于每个请求，应用程序使用现有处理程序集的链接列表的头指针调用各个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="d256b-137">For each request, the application calls each of the the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="d256b-138">每个中间件组件可以向请求处理管道添加一个或多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="d256b-138">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="d256b-139">此过程通过返回对作为列表新头的处理程序的引用来完成。</span><span class="sxs-lookup"><span data-stu-id="d256b-139">This is accomplished by returning a reference to the handler that is the new head of the list.</span></span> <span data-ttu-id="d256b-140">每个处理程序负责记住并调用列表中的下一个处理程序。</span><span class="sxs-lookup"><span data-stu-id="d256b-140">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="d256b-141">使用 ASP.NET Core 时，应用程序的入口点是 `Startup`，不再具有 Global.asax 的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="d256b-141">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="d256b-142">结合使用 OWIN 和 .NET Framework 时，使用的管道应如下所示：</span><span class="sxs-lookup"><span data-stu-id="d256b-142">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

<span data-ttu-id="d256b-143">[!code-csharp[Main](samples/webapi-owin.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-143">[!code-csharp[Main](samples/webapi-owin.cs)]</span></span>

<span data-ttu-id="d256b-144">这会配置默认路由，默认为 XmlSerialization 而不是 Json。</span><span class="sxs-lookup"><span data-stu-id="d256b-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="d256b-145">根据需要向此管道添加其他中间件（加载服务、配置设置、静态文件等）。</span><span class="sxs-lookup"><span data-stu-id="d256b-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="d256b-146">ASP.NET Core 使用相似的方法，但是不依赖 OWIN 处理条目。</span><span class="sxs-lookup"><span data-stu-id="d256b-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="d256b-147">相反，它通过 Program.cs `Main` 方法（类似于控制台应用程序）完成该操作，并加载 `Startup`。</span><span class="sxs-lookup"><span data-stu-id="d256b-147">Instead, that is done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

<span data-ttu-id="d256b-148">[!code-csharp[Main](samples/program.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-148">[!code-csharp[Main](samples/program.cs)]</span></span>

<span data-ttu-id="d256b-149">`Startup` 必须包含 `Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="d256b-149">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="d256b-150">在 `Configure` 中，向管道添加必要的中间件。</span><span class="sxs-lookup"><span data-stu-id="d256b-150">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="d256b-151">在下列示例（位于默认的网站模板）中，使用了多个扩展方法为管道配置对以下内容的支持：</span><span class="sxs-lookup"><span data-stu-id="d256b-151">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="d256b-152">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="d256b-152">BrowserLink</span></span>](http://vswebessentials.com/features/browserlink)
* <span data-ttu-id="d256b-153">错误页</span><span class="sxs-lookup"><span data-stu-id="d256b-153">Error pages</span></span>
* <span data-ttu-id="d256b-154">静态文件</span><span class="sxs-lookup"><span data-stu-id="d256b-154">Static files</span></span>
* <span data-ttu-id="d256b-155">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d256b-155">ASP.NET Core MVC</span></span>
* <span data-ttu-id="d256b-156">标识</span><span class="sxs-lookup"><span data-stu-id="d256b-156">Identity</span></span>

<span data-ttu-id="d256b-157">[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]</span><span class="sxs-lookup"><span data-stu-id="d256b-157">[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]</span></span>

<span data-ttu-id="d256b-158">现在主机和应用程序已分离，这样将来就可以灵活地迁移到其他平台。</span><span class="sxs-lookup"><span data-stu-id="d256b-158">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="d256b-159">注意：若要获取 ASP.NET Core Startup 和中间件的更深入的参考信息，请参阅 [ASP.NET Core 中的 Startup](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="d256b-159">**Note:** For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="d256b-160">存储配置</span><span class="sxs-lookup"><span data-stu-id="d256b-160">Storing Configurations</span></span>
<span data-ttu-id="d256b-161">ASP.NET 支持存储设置。</span><span class="sxs-lookup"><span data-stu-id="d256b-161">ASP.NET supports storing settings.</span></span> <span data-ttu-id="d256b-162">这些设置可用于支持应用程序已部署到的环境（以此用途为例）。</span><span class="sxs-lookup"><span data-stu-id="d256b-162">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="d256b-163">常见做法是将所有的自定义键值对存储在 Web.config 文件的 `<appSettings>` 部分中：</span><span class="sxs-lookup"><span data-stu-id="d256b-163">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

<span data-ttu-id="d256b-164">[!code-xml[Main](samples/webconfig-sample.xml)]</span><span class="sxs-lookup"><span data-stu-id="d256b-164">[!code-xml[Main](samples/webconfig-sample.xml)]</span></span>

<span data-ttu-id="d256b-165">应用程序使用 `System.Configuration` 命名空间中的 `ConfigurationManager.AppSettings` 集合读取这些设置：</span><span class="sxs-lookup"><span data-stu-id="d256b-165">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

<span data-ttu-id="d256b-166">[!code-csharp[Main](samples/read-webconfig.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-166">[!code-csharp[Main](samples/read-webconfig.cs)]</span></span>

<span data-ttu-id="d256b-167">ASP.NET Core 可以将应用程序的配置数据存储在任何文件中，并可在启动中间件的过程中加载它们。</span><span class="sxs-lookup"><span data-stu-id="d256b-167">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="d256b-168">项目模板中使用的默认文件是 appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="d256b-168">The default file used in the project templates is *appsettings.json*:</span></span>

<span data-ttu-id="d256b-169">[!code-json[Main](samples/appsettings-sample.json)]</span><span class="sxs-lookup"><span data-stu-id="d256b-169">[!code-json[Main](samples/appsettings-sample.json)]</span></span>

<span data-ttu-id="d256b-170">将此文件加载到应用程序内的 `IConfiguration` 的实例的过程在 Startup.cs 中完成：</span><span class="sxs-lookup"><span data-stu-id="d256b-170">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

<span data-ttu-id="d256b-171">[!code-csharp[Main](samples/startup-builder.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-171">[!code-csharp[Main](samples/startup-builder.cs)]</span></span>

<span data-ttu-id="d256b-172">应用读取 `Configuration` 来获得这些设置：</span><span class="sxs-lookup"><span data-stu-id="d256b-172">The app reads from `Configuration` to get the settings:</span></span>

<span data-ttu-id="d256b-173">[!code-csharp[Main](samples/read-appsettings.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-173">[!code-csharp[Main](samples/read-appsettings.cs)]</span></span>

<span data-ttu-id="d256b-174">此方法有扩展项，它们可使此过程更加可靠，例如使用[依存关系注入](xref:fundamentals/dependency-injection) (DI) 来加载使用这些值的服务。</span><span class="sxs-lookup"><span data-stu-id="d256b-174">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="d256b-175">DI 方法提供了一组强类型的配置对象。</span><span class="sxs-lookup"><span data-stu-id="d256b-175">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

<span data-ttu-id="d256b-176">注意：若要获取 ASP.NET Core 配置的更深入的参考信息，请参阅 [ASP.NET Core 中的配置](xref:fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="d256b-176">**Note:** For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="d256b-177">本机依存关系注入</span><span class="sxs-lookup"><span data-stu-id="d256b-177">Native Dependency Injection</span></span>
<span data-ttu-id="d256b-178">生成大型可缩放应用程序时，一个重要的目标是将组件和服务松散耦合。</span><span class="sxs-lookup"><span data-stu-id="d256b-178">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="d256b-179">[依存关系注入](xref:fundamentals/dependency-injection)是用于实现此目标的热门技术，并且它是 ASP.NET Core 的本机组件。</span><span class="sxs-lookup"><span data-stu-id="d256b-179">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it is a native component of ASP.NET Core.</span></span>

<span data-ttu-id="d256b-180">在 ASP.NET 应用程序中，开发人员依赖第三方库实现依存关系注入。</span><span class="sxs-lookup"><span data-stu-id="d256b-180">In ASP.NET applications, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="d256b-181">其中的一个库是 Microsoft 模式和做法提供的 [Unity](https://github.com/unitycontainer/unity)。</span><span class="sxs-lookup"><span data-stu-id="d256b-181">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span> 

<span data-ttu-id="d256b-182">实现打包 `UnityContainer` 的 `IDependencyResolver` 是使用 Unity 设置依存关系注入的一个示例：</span><span class="sxs-lookup"><span data-stu-id="d256b-182">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

<span data-ttu-id="d256b-183">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-183">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]</span></span>

<span data-ttu-id="d256b-184">创建 `UnityContainer` 的实例，注册服务，然后将 `HttpConfiguration` 的依赖关系解析程序设置为容器的 `UnityResolver` 新实例：</span><span class="sxs-lookup"><span data-stu-id="d256b-184">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

<span data-ttu-id="d256b-185">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-185">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]</span></span>

<span data-ttu-id="d256b-186">在必要时注入 `IProductRepository`：</span><span class="sxs-lookup"><span data-stu-id="d256b-186">Inject `IProductRepository` where needed:</span></span>

<span data-ttu-id="d256b-187">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-187">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]</span></span>

<span data-ttu-id="d256b-188">由于依存关系注入是 ASP.NET Core 的组成部分，因此可以在 Startup.cs 的 `ConfigureServices` 方法中添加你的服务：</span><span class="sxs-lookup"><span data-stu-id="d256b-188">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="d256b-189">[!code-csharp[Main](samples/configure-services.cs)]</span><span class="sxs-lookup"><span data-stu-id="d256b-189">[!code-csharp[Main](samples/configure-services.cs)]</span></span>

<span data-ttu-id="d256b-190">可在任意位置注入存储库，Unity 亦是如此。</span><span class="sxs-lookup"><span data-stu-id="d256b-190">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="d256b-191">注意：若要获取 ASP.NET Core 中的依存关系注入的深入的参考信息，请参阅 [ASP.NET Core 中的依存关系注入](xref:fundamentals/dependency-injection#replacing-the-default-services-container)</span><span class="sxs-lookup"><span data-stu-id="d256b-191">**Note:** For an in-depth reference to dependency injection in ASP.NET Core, see [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#replacing-the-default-services-container)</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="d256b-192">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="d256b-192">Serving Static Files</span></span>
<span data-ttu-id="d256b-193">Web 开发的一个重要环节是提供客户端静态资产的功能。</span><span class="sxs-lookup"><span data-stu-id="d256b-193">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="d256b-194">HTML、CSS、Javascript 和图像是最常见的静态文件示例。</span><span class="sxs-lookup"><span data-stu-id="d256b-194">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="d256b-195">这些文件需要保存在应用（或 CDN）的发布位置中，并且需要引用它们，以便请求可以加载这些文件。</span><span class="sxs-lookup"><span data-stu-id="d256b-195">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="d256b-196">在 ASP.NET Core 中，此过程发生了变化。</span><span class="sxs-lookup"><span data-stu-id="d256b-196">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="d256b-197">在 ASP.NET 中，静态文件存储在各种目录中，并在视图中进行引用。</span><span class="sxs-lookup"><span data-stu-id="d256b-197">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="d256b-198">在 ASP.NET Core 中，静态文件存储在“Web 根”（&lt;内容根&gt;/wwwroot）中，除非另有配置。</span><span class="sxs-lookup"><span data-stu-id="d256b-198">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="d256b-199">通过从 `Startup.Configure` 调用 `UseStaticFiles` 扩展方法将这些文件加载到请求管道中：</span><span class="sxs-lookup"><span data-stu-id="d256b-199">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

<span data-ttu-id="d256b-200">[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="d256b-200">[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span></span>

<span data-ttu-id="d256b-201">注意：如果面向 .NET Framework，则安装 NuGet 包 `Microsoft.AspNetCore.StaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="d256b-201">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="d256b-202">例如，可以通过浏览器从类似 `http://<app>/images/<imageFileName>` 的位置访问 wwwroot/images 文件夹中的图像资产。</span><span class="sxs-lookup"><span data-stu-id="d256b-202">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="d256b-203">注意：若要获取在 ASP.NET Core 中提供静态文件的更深入的参考信息，请参阅[关于在 ASP.NET Core 中使用静态文件的说明](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="d256b-203">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see [Introduction to working with static files in ASP.NET Core](xref:fundamentals/static-files).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d256b-204">其他资源</span><span class="sxs-lookup"><span data-stu-id="d256b-204">Additional Resources</span></span>
* [<span data-ttu-id="d256b-205">将库移植到 .NET Core</span><span class="sxs-lookup"><span data-stu-id="d256b-205">Porting Libraries to .NET Core</span></span>](https://docs.microsoft.com/dotnet/core/porting/libraries)