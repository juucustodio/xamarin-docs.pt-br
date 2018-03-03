---
title: Backgrounding
description: "Plano de fundo de processamento ou backgrounding é o processo de permitir que aplicativos executar tarefas em segundo plano enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento de iOS em segundo plano."
ms.topic: article
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 2bba7c0908fb78ca199cc654adad645afaf47a02
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="backgrounding"></a>Backgrounding

_Plano de fundo de processamento ou backgrounding é o processo de permitir que aplicativos executar tarefas em segundo plano enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento de iOS em segundo plano._

Backgrounding em aplicativos móveis é bem diferente do que o conceito tradicional de multitarefa na área de trabalho. Computadores desktop têm uma variedade de recursos disponíveis para um aplicativo, incluindo memória, energia e espaço na tela. Os aplicativos são capazes de executar lado a lado e permanecem funcionais e utilizável. Em um dispositivo móvel, os recursos são muito mais limitados. É difícil Mostrar mais de um aplicativo em uma tela pequena e executando vários aplicativos à velocidade total seria esgotar a bateria. Backgrounding é um compromisso constante entre fornecendo os recursos para executar as tarefas em segundo plano que precisam para realizar também de aplicativos e manter o dispositivo e o aplicativo foregrounded responsivo. IOS e Android tem provisões para backgrounding, mas tratam de maneiras muito diferentes.

No iOS, backgrounding é reconhecido como um estado de aplicativo e aplicativos são movidos para dentro e fora o estado de plano de fundo, dependendo do comportamento do aplicativo e o usuário. iOS também oferece várias opções para conectar um aplicativo para ser executado em segundo plano, incluindo pedir que o sistema operacional para o tempo para concluir uma tarefa importante, operação como um tipo de aplicativo necessário do plano de fundo conhecido, e atualizar o conteúdo de um aplicativo em designado intervalos.

Este guia e que acompanha explicações passo a passo, vamos aprender a executar tarefas de aplicativo em segundo plano. Abordaremos principais conceitos e as práticas recomendadas e, em seguida, passar pela criação de um aplicativo do mundo real que recebe atualizações do local no plano de fundo.

## <a name="contents"></a>Conteúdo

1.  [Introdução ao Backgrounding no iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Demonstração de ciclo de vida do aplicativo](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [iOS Backgrounding técnicas](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Explicações passo a passo: Backgrounding no iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [iOS Backgrounding orientação](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Resumo

Neste guia, apresentamos as diferentes maneiras de fazer o processamento em segundo plano no iOS. Estamos coberto iOS estados do aplicativo e examinou a função backgrounding desempenha no ciclo de vida do aplicativo iOS. Além disso, aprendemos como estamos pôde registrar tarefas individuais ou aplicativos completos para operar em segundo plano em iOS. Por fim, podemos reforçado nossa compreensão do backgrounding no iOS criando aplicativos que realizar atualizações em segundo plano.



## <a name="related-links"></a>Links relacionados

- [Backgrounding no Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (sample)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Local (exemplo)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Transferência em segundo plano simples (exemplo)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [Execução de plano de fundo do iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
