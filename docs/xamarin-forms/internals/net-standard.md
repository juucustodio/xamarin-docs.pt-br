---
title: Suporte do .NET Standard 2,0 no Xamarin. Forms
description: Este artigo explica como converter um aplicativo Xamarin. Forms para usar o .NET Standard 2,0. .NET Standard é uma especificação de APIs do .NET que devem estar disponíveis em todas as implementações do .NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: eaef607e3e6095ccc7dfb1f5831d2384d11cab5f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760055"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Suporte do .NET Standard 2,0 no Xamarin. Forms

_Este artigo explica como converter um aplicativo Xamarin. Forms para usar o .NET Standard 2,0._

.NET Standard é uma especificação de APIs do .NET que devem estar disponíveis em todas as implementações do .NET. Ele facilita o compartilhamento de código entre aplicativos de área de trabalho, aplicativos móveis e jogos e serviços de nuvem, trazendo APIs idênticas para as diferentes plataformas. Para obter informações sobre as plataformas com suporte pelo .NET Standard, consulte [suporte à implementação do .net](/dotnet/standard/net-standard#net-implementation-support).

As bibliotecas de .NET Standard são a substituição para PCL (bibliotecas de classes portáteis). No entanto, uma biblioteca que tem como alvo .NET Standard ainda é uma PCL e é conhecida como uma PCL baseada em .NET Standard. Determinados perfis PCL são mapeados para .NET Standard versões e para perfis que têm um mapeamento, os dois tipos de biblioteca poderão fazer referência entre si. Para obter mais informações, consulte [compatibilidade de PCL](/dotnet/standard/net-standard#pcl-compatibility).

O xamarin. Forms 2,4 permite que os aplicativos Xamarin. Forms direcionem .NET Standard 2,0, substituindo o PCL por uma biblioteca .NET Standard 2,0. Isso pode ser feito da seguinte maneira:

- Verifique se o [.NET Core 2,0](https://www.microsoft.com/net/download/core) está instalado.
- Atualize a solução Xamarin. Forms para usar o Xamarin. Forms 2,4 ou superior.
- Adicione uma biblioteca de .NET Standard à solução, que é direcionada .NET Standard 2,0.
- Exclua a classe que é adicionada à biblioteca de .NET Standard.
- Adicione o pacote NuGet Xamarin. Forms 2,4 (ou superior) à biblioteca .NET Standard.
- Nos projetos da plataforma, adicione uma referência à biblioteca de .NET Standard e remova a referência ao projeto PCL que contém a lógica da interface do usuário Xamarin. Forms.
- Copie os arquivos do projeto PCL para a biblioteca de .NET Standard.
- Remova o projeto PCL que contém a lógica da interface do usuário do Xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
