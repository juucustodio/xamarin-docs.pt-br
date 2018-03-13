---
title: SiriKit
description: "Este artigo mostra como usar SiriKit em um aplicativo xamarin para fornecer serviços que são acessíveis para o usuário usando Siri em um dispositivo iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9d7773be1244b0ba4e1a57c8a1efbddf02396138
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="sirikit"></a>SiriKit

_Este artigo mostra como usar SiriKit em um aplicativo xamarin para fornecer serviços que são acessíveis para o usuário usando Siri em um dispositivo iOS._

Novo para iOS 10, SiriKit permite que um aplicativo iOS fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS usando extensões de aplicativo e o novo **tentativas** e **propósitos de interface do usuário** estruturas.

Siri funciona com o conceito de **domínios**, saber de grupos de ações para tarefas relacionadas. Cada interação de um aplicativo com Siri deve estar em um de seus domínios de serviço conhecido da seguinte maneira:

- Áudio ou vídeo chamada.
- Reserva uma jornada.
- Gerenciando exercícios.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação de Siri que envolvem um dos serviços de uma extensão aplicativo, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário junto com os dados de suporte. A extensão do aplicativo, em seguida, gera apropriada **resposta** de objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Noções básicas sobre os conceitos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Este artigo aborda os principais conceitos que serão necessários para trabalhar com SiriKit em um aplicativo xamarin. Ele aborda as novas tentativas e tentativas de pontos de extensão de interface do usuário e como elas funcionam com o aplicativo e o vocabulário do usuário para abrir um aplicativo em Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementar SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Este artigo aborda as etapas necessárias para implementar SiriKit suporte em um aplicativos xamarin. O desenvolvedor deve ler o guia de Noções básicas sobre conceitos de SiriKit acima antes de tentar adicionar SiriKit suporte a um aplicativo, como chave conceitos são abordados que serão necessário para uma implementação bem-sucedida.





## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência do Framework de interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
