---
title: SiriKit no Xamarin. iOS
description: Este artigo mostra como usar o SiriKit em um aplicativo Xamarin. iOS para fornecer serviços que são acessíveis para o usuário usando o Siri em um dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0438ea08bbdcbf0ce3c64e15192cbb90cd835b00
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654013"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit no Xamarin. iOS

_Este artigo mostra como usar o SiriKit em um aplicativo Xamarin. iOS para fornecer serviços que são acessíveis para o usuário usando o Siri em um dispositivo iOS._

Novo no iOS 10, o SiriKit permite que um aplicativo iOS forneça serviços que podem ser acessados pelo usuário usando o Siri e o aplicativo Maps em um dispositivo iOS usando extensões de aplicativo e **as novas estruturas** de **interface do usuário** de tentativas e intenções.

O Siri trabalha com o conceito de **domínios**, grupos de ações conhecidas para tarefas relacionadas. Cada interação que um aplicativo tem com siri deve se enquadrar em um de seus domínios de serviço conhecidos da seguinte maneira:

- Chamada de áudio ou vídeo.
- Reserva de um Ride.
- Gerenciando exercícios.
- Mensagens.
- Pesquisando fotos.
- Enviando ou recebendo pagamentos.

Quando o usuário faz uma solicitação de Siri envolvendo um dos serviços de extensão de aplicativo, o SiriKit envia a extensão um objeto de **intenção** que descreve a solicitação do usuário junto com quaisquer dados de suporte. Em seguida, a extensão do aplicativo gera o objeto de **resposta** apropriado para a **intenção**determinada, detalhando como a extensão pode lidar com a solicitação.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Noções básicas sobre os conceitos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo Xamarin. iOS. Ele aborda os novos pontos de extensão de interface do usuário e tentativas e como eles funcionam com o aplicativo e o vocabulário do usuário para abrir um aplicativo para Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementar SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Este artigo aborda as etapas necessárias para implementar o suporte do SiriKit em aplicativos Xamarin. iOS. O desenvolvedor deve ler o guia de conceitos de compreensão do SiriKit acima antes de tentar adicionar o suporte do SiriKit a um aplicativo, já que os principais conceitos são cobertos, que serão necessários para uma implementação bem-sucedida.





## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guia de programação do SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência da estrutura da interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
