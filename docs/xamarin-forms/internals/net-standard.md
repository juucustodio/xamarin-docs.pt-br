---
title: 2.0 suporte ao .NET standard no xamarin. Forms
description: Este artigo explica como converter um aplicativo xamarin. Forms para usar o .NET Standard 2.0. .NET standard é uma especificação de APIs .NET que devem estar disponíveis em todas as implementações do .NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c3e46592bb8760ff85eaeb5dce119897a97dfe89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293279"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>2.0 suporte ao .NET standard no xamarin. Forms

_Este artigo explica como converter um aplicativo xamarin. Forms para usar o .NET Standard 2.0._

.NET standard é uma especificação de APIs .NET que devem estar disponíveis em todas as implementações do .NET. Ele torna mais fácil de compartilhar código entre aplicativos da área de trabalho, aplicativos móveis e jogos e serviços de nuvem, reunindo APIs idêntico para as plataformas diferentes. Para obter informações sobre as plataformas com suporte pelo .NET Standard, consulte [suporte à implementação do .NET](/dotnet/standard/net-standard#net-implementation-support).

Bibliotecas do .NET standard são a substituição para Portable Class Libraries (PCL). No entanto, uma biblioteca direcionada ao .NET Standard ainda é uma PCL e é conhecida como uma PCL baseada no .NET Standard. Determinados perfis de PCL são mapeados para versões do .NET Standard e para os perfis que têm um mapeamento, os tipos de dois biblioteca poderão fazer referência uns aos outros. Para obter mais informações, consulte [compatibilidade com PCL](/dotnet/standard/net-standard#pcl-compatibility).

2.4 de xamarin. Forms permite que os aplicativos xamarin. Forms destino .NET Standard 2.0, substituindo o PCL com uma biblioteca .NET Standard 2.0. Isso pode ser feito da seguinte maneira:

- Certifique-se [.NET Core 2.0](https://www.microsoft.com/net/download/core) está instalado.
- Atualize a solução do xamarin. Forms para usar o xamarin. Forms 2.4 ou superior.
- Adicione uma biblioteca .NET Standard à solução, que tem como alvo o .NET Standard 2.0.
- Exclua a classe que é adicionada à biblioteca do .NET Standard.
- Adicione o pacote do NuGet xamarin. Forms 2,4 (ou superior) para a biblioteca .NET Standard.
- Em projetos da plataforma, adicione uma referência à biblioteca do .NET Standard e remova a referência ao projeto da PCL que contém a lógica de interface de usuário do xamarin. Forms.
- Copie os arquivos do projeto PCL para a biblioteca .NET Standard.
- Remova o projeto PCL que contém a lógica de interface de usuário do xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
