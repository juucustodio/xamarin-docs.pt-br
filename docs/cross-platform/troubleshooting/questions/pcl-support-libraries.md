---
title: Como posso exibir as bibliotecas com suporte em um PCL?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: davidortinau
ms.author: daortin
ms.date: 07/27/2018
ms.openlocfilehash: 6d6519fc058c91a0f3519be034f8840469f87f0e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013333"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Como posso exibir as bibliotecas com suporte em um PCL?

- Você pode encontrar uma visão geral dos vários recursos com suporte nas várias plataformas de destino PCL na parte *recursos com suporte* desta página: [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- Outra opção é usar o [analisador de portabilidade do .net](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) para avaliar se sua biblioteca existente pode ser convertida em um perfil PCL.

- Uma terceira possibilidade é procurar o conteúdo do perfil real que você possa usar. Usando o perfil 78, por exemplo, você pode ir aqui: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` e exibir todos os assemblies dentro dele.

Seja qual for o método escolhido, observe que algumas funcionalidades têm que ser baixadas por meio do NuGet e da biblioteca Microsoft BCL.
