---
title: Com suporte de assemblies do Xamarin para tvOS
description: Para ajudar a esclarecer os recursos disponíveis para aplicativos de tvOS, este documento fornece uma lista de assemblies de suporte para o Xamarin para desenvolvimento de tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 1749de49607596fa2b8e555fec471af1d18b8ce9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116674"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Com suporte de assemblies do Xamarin para tvOS

## <a name="supported-assemblies"></a>Assemblies com suporte

Esta é uma lista dos assemblies de suporte para o Xamarin para seus aplicativos xamarin. tvos. A lista detalhada desses esteja listada abaixo.  Incluem alguns notáveis omissões `System.EnterpriseServices`, a pilha do ASP.NET e Windows. Forms.

|Assembly|Added|Compatibilidade de API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Para autores de compilador.|
|Mono.Data.Sqlite.dll|1.2|Provedor ADO.NET para SQLite; ver [limitações](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Suporte de protocolo TDS; usado para [System.Data.SqlClient](xref:System.Data.SqlClient) suportem [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|APIs de criptografia.|
|monotouch.dll|1.0|Esse assembly contém o [c# associação para a API CocoaTouch](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|APIs, orientado ao objeto de OpenGL/OpenAL [estendida para oferecer suporte a dispositivos iPhone](https://developer.xamarin.com/api/namespace/OpenGLES/).|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx), [com algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3. x|Cliente oData completa.|
|System.Drawing|1.0|System. Drawing API - API clássica apenas.<br />_Não há suporte para System. Drawing na API unificada do xamarin. Mac .NET 4.5 ou estruturas móveis._|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) de pilha como presente no [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir: <ul><li>Sistema</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); faz parte do [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) dão suporte.|
|System.Web.Services|1.1|[Serviços básicos da Web](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) do perfil .NET 3.5, com os recursos de servidor removido.|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

Além das associações de Mac, xamarin. tvos pode consumir [bibliotecas de classes portátil .NET](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Links relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
