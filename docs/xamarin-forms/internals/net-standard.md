---
title: Suporte de 2.0 padrão do .NET em xamarin. Forms
description: Este artigo explica como converter um aplicativo xamarin. Forms para usar o .NET 2.0 padrão. .NET padrão é uma especificação de APIs do .NET que devem estar disponíveis em todas as implementações de .NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: dc90155c79d1d2850281744c4c9aac70cbd7ecc3
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242349"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Suporte de 2.0 padrão do .NET em xamarin. Forms

_Este artigo explica como converter um aplicativo xamarin. Forms para usar o .NET 2.0 padrão._

.NET padrão é uma especificação de APIs do .NET que devem estar disponíveis em todas as implementações de .NET. Ele torna mais fácil compartilhar código em aplicativos de área de trabalho, aplicativos móveis e jogos e serviços de nuvem, colocando APIs idênticos para diferentes plataformas. Para obter informações sobre as plataformas com suporte pelo .NET padrão, consulte [suporte à implementação do .NET](/dotnet/standard/net-standard#net-implementation-support/).

As bibliotecas .NET padrão são a substituição para bibliotecas de classe portáteis (PCL). No entanto, uma biblioteca que tem como destino .NET padrão ainda é um PCL e é conhecida como um PCL baseados em .NET padrão. Determinados perfis PCL são mapeados para as versões padrão do .NET, e para os perfis que tem um mapeamento, os tipos de dois biblioteca será capazes de fazer referência uns aos outros. Para obter mais informações, consulte [compatibilidade PCL](/dotnet/standard/net-standard#pcl-compatibility).

Xamarin. Forms 2.4 permite que aplicativos xamarin. Forms destino .NET 2.0 padrão, substituindo o PCL por uma biblioteca .NET 2.0 padrão. Isso pode ser feito da seguinte maneira:

- Certifique-se de [.NET Core 2.0](https://www.microsoft.com/net/download/core) está instalado.
- Atualize a solução xamarin. Forms para usar xamarin. Forms 2.4 ou maior.
- Adicione uma biblioteca .NET padrão para a solução, que tem como alvo o .NET 2.0 padrão.
- Exclua a classe que é adicionada à biblioteca .NET padrão.
- Adicione o pacote do NuGet xamarin. Forms 2.4 (ou superior) para a biblioteca .NET padrão.
- Nos projetos de plataforma, adicione uma referência à biblioteca do .NET padrão e remova a referência ao projeto PCL que contém a lógica de interface de usuário do xamarin. Forms.
- Copie os arquivos do projeto PCL à biblioteca .NET padrão.
- Remova o projeto PCL que contém a lógica de interface de usuário do xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
