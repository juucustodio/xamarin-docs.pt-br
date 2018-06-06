---
title: Assemblies disponíveis
description: Este documento lista os assemblies disponíveis para uso em xamarin, xamarin e Xamarin.Mac. Também links para documentação sobre as bibliotecas padrão do .NET e bibliotecas de classes portáteis.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: b73a818d3864c7c4d1d776e104d95090e87f5877
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781901"
---
# <a name="available-assemblies"></a>Assemblies disponíveis

Xamarin, xamarin e Xamarin.Mac todos os fornecidos com dezenas de assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, plataformas Xamarin também é um subconjunto estendido de vários Silverlight e área de trabalho assemblies do .NET.

Xamarin plataformas não são ABI compatível com existentes assemblies compilados para um perfil diferente. Você deve recompilar o código-fonte para gerar assemblies direcionando o perfil correto (assim como você precisa recompilar o código-fonte para o destino do Silverlight e do .NET 3.5 separadamente).

Xamarin.Mac aplicativos podem ser compilados em três modos: um que usa Xamarin do curadoria perfil móvel, o Xamarin.Mac .NET Framework 4.5 que permite que você direcione assemblies existentes de área de trabalho completa e um sem suporte que usa a API .NET encontrado em um sistema Mono instalação. Para obter mais informações, consulte nosso [estrutura de destino](~/mac/platform/target-framework.md) documentação.


## <a name="net-standard-libraries"></a>Bibliotecas padrão do .NET

Além de Mac, iOS e Android associações, Xamarin projetos podem consumir [bibliotecas .NET padrão](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis
 
Projetos do Xamarin também podem consumir [bibliotecas de classes portáteis do .NET](~/cross-platform/app-fundamentals/pcl.md), embora essa tecnologia está sendo substituída pela .NET padrão.

## <a name="supported-assemblies"></a>Assemblies com suporte

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilidade de API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|Inclui CJK, Ásia, outros, raros, oeste|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Provedor ADO.NET para SQLite; Consulte limitações.|✓|✓|✓|
> |Mono.Data.Tds.dll|Suporte de protocolo TDS; usado para [SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/) suportem [System. Data](https://developer.xamarin.com/api/namespace/System.Data/).|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|APIs de criptografia.|✓|✓|✓|
> |monotouch.dll|Este assembly contém a associação para a API CocoaTouch c#. Isso só está disponível dentro de projetos de iOS clássico.|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|O objeto OpenGL/OpenAL orientada a APIs, estendido para oferecer suporte a dispositivos iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx) , com [algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|Cliente oData completo.|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|Pilha do WCF como presentes no [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir: <br />Sistema<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); faz parte do [System. Data](~/ios/data-cloud/system.data.md) suporte.|✓|✓|✓|
> |System.Web.&#8203;Services.dll|Serviços Web básico do perfil do .NET 3.5, com os recursos de servidor removido.|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Este assembly contém a associação para a API CocoaTouch c#. Isso é usado apenas em projetos do iOS unificada.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Para autores de compilador.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System. Drawing API - somente API clássica. Não há suporte para System. Drawing na API unificada para o Xamarin.Mac .NET 4.5 ou estruturas móveis. Suporte de System. Drawing pode ser adicionado para iOS e OS X usando o [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) biblioteca|✓| |✓|
