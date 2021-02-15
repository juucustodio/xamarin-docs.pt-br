---
title: Plano de fundo no Xamarin. iOS
description: O processamento em segundo plano ou o plano de fundo é o processo de permitir que aplicativos executem tarefas em segundo plano enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento em segundo plano no iOS.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 3d50bf91502e7a3b7331348a61af50a5c789f838
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432422"
---
# <a name="backgrounding-in-xamarinios"></a>Plano de fundo no Xamarin. iOS

_O processamento em segundo plano ou o plano de fundo é o processo de permitir que aplicativos executem tarefas em segundo plano enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento em segundo plano no iOS._

O plano de fundo em aplicativos móveis é fundamentalmente diferente do conceito tradicional de multitarefas na área de trabalho. Os computadores desktop têm uma variedade de recursos disponíveis para um aplicativo, incluindo espaço real da tela, energia e memória. Os aplicativos podem ser executados lado a lado e permanecem em bom desempenho e utilizáveis. Em um dispositivo móvel, os recursos são muito mais limitados. É difícil mostrar mais de um aplicativo em uma tela pequena, e executar vários aplicativos em plena velocidade drenaria a bateria. O plano de fundo é um comprometimento constante entre dar aos aplicativos os recursos para executar as tarefas em segundo plano que eles precisam executar bem e manter o aplicativo em primeiro plano e o dispositivo responsivo. O iOS e o Android têm provisões para o plano de fundo, mas eles o tratam de maneiras muito diferentes.

No iOS, o plano de fundo é reconhecido como um estado do aplicativo e os aplicativos são movidos para dentro e para fora do estado do plano de fundo, dependendo do comportamento do aplicativo e do usuário. o iOS também oferece várias opções para conectar um aplicativo a ser executado em segundo plano, incluindo pedir ao sistema operacional o tempo para concluir uma tarefa importante, operar como um tipo de aplicativo conhecido em segundo plano necessário e atualizar o conteúdo de um aplicativo em intervalos designados.

Neste guia e orientações detalhadas, vamos aprender a executar tarefas de aplicativo em segundo plano. Abordaremos os principais conceitos e práticas recomendadas e, em seguida, percorrerei a criação de um aplicativo do mundo real que recebe atualizações de local em segundo plano.

## <a name="contents"></a>Sumário

1. [Introdução à operação em segundo plano no iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [Demonstração do ciclo de vida do aplicativo](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [Técnicas de operação em segundo plano no iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [Passo a passo: backgrounding no iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [Diretrizes de operação em segundo plano do iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Resumo

Neste guia, apresentamos as diferentes maneiras de fazer processamento em segundo plano no iOS. Abordamos os Estados do aplicativo iOS e examinamos as reproduções de plano de fundo de função no ciclo de vida do aplicativo iOS. Além disso, aprendemos como poderíamos registrar tarefas individuais ou aplicativos inteiros para operar em segundo plano no iOS. Por fim, reforçamos nossa compreensão do plano de fundo no iOS criando aplicativos que executam atualizações em segundo plano.

## <a name="related-links"></a>Links Relacionados

- [Plano de fundo no Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (exemplo)](/samples/xamarin/ios-samples/lifecycledemo)
- [Local (exemplo)](/samples/xamarin/ios-samples/location)
- [Transferência simples em segundo plano (exemplo)](/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [Execução em segundo plano do iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)