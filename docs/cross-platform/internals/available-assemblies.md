---
title: Assemblies disponíveis
description: Este documento lista os assemblies disponíveis para uso no Xamarin. iOS, Xamarin. Android e Xamarin. Mac. Ele também fornece links para a documentação sobre bibliotecas de .NET Standard e bibliotecas de classes portáteis.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: cb5c4a463a8368d6a82d89baba08145f161a95d6
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457933"
---
# <a name="available-assemblies"></a>Assemblies disponíveis

O xamarin. iOS, o Xamarin. Android e o Xamarin. Mac são fornecidos com mais de uma dúzia de assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, as plataformas Xamarin também são um subconjunto estendido de vários assemblies do Silverlight e do .NET da área de trabalho

As plataformas Xamarin não são compatíveis com ABI com assemblies existentes compilados para um perfil diferente. Você deve recompilar o código-fonte para gerar assemblies destinados ao perfil correto (assim como você precisa recompilar o código-fonte para o Silverlight e o .NET 3,5 de destino separadamente).

Os aplicativos Xamarin. Mac podem ser compilados em três modos: um que usa o perfil móvel organizado do Xamarin, a estrutura Xamarin. Mac .NET 4,5, que permite que você direcione assemblies de área de trabalho completos existentes e um que usa a API do .NET encontrada em uma instalação do sistema mono. Para obter mais informações, consulte nossa documentação de [estruturas de destino](~/mac/platform/target-framework.md) .

## <a name="net-standard-libraries"></a>Bibliotecas de .NET Standard

Além das associações do iOS, do Android e do Mac, os projetos do Xamarin podem consumir [.net Standard bibliotecas](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

Os projetos do Xamarin também podem consumir [bibliotecas de classes portáteis do .net](~/cross-platform/app-fundamentals/pcl.md), embora essa tecnologia esteja sendo substituída em favor da .net Standard.

## <a name="supported-assemblies"></a>Assemblies com suporte

Esses são os assemblies disponíveis no **Gerenciador de referências > assemblies > Framework** (Visual Studio 2017) e **Editar referências > pacotes** (Visual Studio para Mac) e sua compatibilidade com as plataformas Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilidade de API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |l18N.dll|Inclui CJK, Oriente Médio, outro, raro, oeste|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Microsoft.CSharp.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.CSharp.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.Data.Sqlite.dll|Provedor de ADO.NET para SQLite; consulte limitações.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.Data.Tds.dll|Suporte a protocolo TDS; usado para suporte a [System. Data. SqlClient](xref:System.Data.SqlClient) em [System. Data](xref:System.Data).|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono. Dynamic. &#8203;Interpreter.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |Mono.Security.dll|APIs de criptografia.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |monotouch.dll|Esse assembly contém a ligação C# com a API CocoaTouch. Isso só está disponível em projetos clássicos do iOS.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |Dialog-1.dll de MonoTouch. &#8203;| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |NUnitLite.dll de MonoTouch. &#8203;| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |mscorlib.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |OpenTK-1.0.dll|As APIs do OpenGL/com orientação a objeto aberto, estendidas para fornecer suporte a dispositivos iPhone.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95)), além de tipos dos seguintes namespaces:<br />System.Collections.Specialized<br />System. &#8203;ComponentModel<br />System. ComponentModel. Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System .net. cache<br />System.Net.Mail<br />System .net. MIME<br />System.Net. &#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System. Runtime. &#8203;interoperations<br />System.Runtime.Versioning<br />System. Security. &#8203;AccessControl<br />System.Security.Authentication<br />Criptografia de System. Security. &#8203;<br />System.Security.Permissions<br />System.Threading<br />System. Timers|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;ComponentModel. &#8203;Composition.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;ComponentModel. &#8203;DataAnnotations.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Core.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Data.dll|[.Net 3,5](/previous-versions/ms229335(v=vs.100)) , com [algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Serviços System. Data. &#8203;. &#8203;Client.dll|Cliente oData completo.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Compactação de System.IO. &#8203;| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.IO. &#8203;compactação. &#8203;FileSystem| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Json.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Net. &#8203;Http.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;Numerics.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. Runtime. &#8203;Serialization.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;ServiceModel.dll|Pilha do WCF como presente no [Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;ServiceModel. &#8203;Internals.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;ServiceModel. &#8203;Web.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95)), além de tipos dos seguintes namespaces: <br />Sistema<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;Transactions.dll|[.Net 3,5](/previous-versions/ms229335(v=vs.100)); parte do suporte a [System. Data](~/ios/data-cloud/system.data.md) .|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. Web. &#8203;Services.dll|Serviços Web básicos do perfil .NET 3,5, com os recursos do servidor removidos.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;Windows.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;Xml.dll|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Xml. &#8203;Linq.dll|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Xml.Serialization.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Xamarin.iOS.dll|Esse assembly contém a ligação C# com a API CocoaTouch. Isso só é usado em projetos iOS unificados.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |Java.Interop.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono.Android.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono. Android. &#8203;Export.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono.Posix.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |System. &#8203;EnterpriseServices.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Xamarin. Android. &#8203;NUnitLite.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono. CompilerServices. &#8203;SymbolWriter.dll|Para escritores de compilador.| | |![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Xamarin.Mac.dll| | | |![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System. &#8203;Drawing.dll|Não há suporte para System. Drawing no API Unificada para o Xamarin. Mac, o .NET 4,5 ou para as estruturas móveis. O suporte a System. Drawing pode ser adicionado ao iOS e ao macOS usando a biblioteca [sysdrawing-CoreGraphics](https://github.com/mono/sysdrawing-coregraphics)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| |![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|