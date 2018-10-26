---
title: SiriKit no xamarin. IOS
description: Este artigo mostra como usar o SiriKit em um aplicativo xamarin. IOS para fornecer serviços que são acessíveis para o usuário usando o Siri em um dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f7cbb3f7d9e448947ec8163a8660616910e750f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117519"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit no xamarin. IOS

_Este artigo mostra como usar o SiriKit em um aplicativo xamarin. IOS para fornecer serviços que são acessíveis para o usuário usando o Siri em um dispositivo iOS._

Novo para o iOS 10, o SiriKit permite que um aplicativo iOS fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS usando extensões de aplicativo e o novo **intenções** e **IU de Intents** estruturas.

Siri funciona com o conceito de **domínios**, sabe de grupos de ações para tarefas relacionadas. Cada interação de um aplicativo com Siri deve se enquadram em um de seus domínios de serviço conhecido da seguinte maneira:

- Chamada de vídeo ou de áudio.
- Uma jornada de reserva.
- Gerenciando exercícios físicos.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação do Siri que envolvem um dos serviços de uma extensão aplicativo, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário, juntamente com quaisquer dados de suporte. A extensão de aplicativo, em seguida, gera apropriado **resposta** do objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Noções básicas sobre os conceitos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo xamarin. IOS. Ele aborda o novo intenções e propósitos de pontos de extensão de interface do usuário e como eles funcionam com o aplicativo e o vocabulário de usuário para abrir um aplicativo em Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementar SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Este artigo aborda as etapas necessárias para implementar SiriKit suporte em um aplicativos xamarin. IOS. O desenvolvedor deve ler o guia de Noções básicas sobre os conceitos de SiriKit acima antes de tentar adicionar SiriKit suporte a um aplicativo, como chave conceitos são abordados que serão necessário para uma implementação bem-sucedida.





## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência de estrutura de interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
