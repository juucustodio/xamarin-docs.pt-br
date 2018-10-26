---
title: Backgrounding no xamarin. IOS
description: Plano de fundo de processamento ou backgrounding é o processo de permitir que aplicativos executar tarefas em segundo plano, enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento no iOS em segundo plano.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: a4f5112b6e77ab6e00453c19c766d1e905df1144
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122771"
---
# <a name="backgrounding-in-xamarinios"></a>Backgrounding no xamarin. IOS

_Plano de fundo de processamento ou backgrounding é o processo de permitir que aplicativos executar tarefas em segundo plano, enquanto outro aplicativo está em execução em primeiro plano. Este guia serve como uma introdução ao processamento no iOS em segundo plano._

Backgrounding em aplicativos móveis é fundamentalmente diferente do conceito tradicional de multitarefa na área de trabalho. Computadores desktop têm uma variedade de recursos disponíveis para um aplicativo, incluindo memória, energia e espaço na tela. Os aplicativos são capazes de executar lado a lado e permanecem com bom desempenho e utilizável. Em um dispositivo móvel, os recursos são muito mais limitados. É difícil Mostrar mais de um aplicativo em uma tela pequena e executando vários aplicativos em velocidade total seria consomem a bateria. Backgrounding é um compromisso constante entre fornecendo os recursos para executar as tarefas em segundo plano necessárias para funcionar bem a aplicativos e manter o dispositivo e o aplicativo foregrounded responsiva. IOS e Android têm provisões para backgrounding, mas eles manipulá-lo de maneiras muito diferentes.

No iOS, backgrounding é reconhecido como um estado de aplicativo e aplicativos são movidos para dentro e fora o estado do plano de fundo, dependendo do comportamento do aplicativo e o usuário. iOS também oferece várias opções para conectar um aplicativo para ser executado em segundo plano, incluindo pedir o sistema operacional para o tempo para concluir uma tarefa importante, operando como um tipo de aplicativo necessário em segundo plano conhecido, e atualizar o conteúdo de um aplicativo em designado intervalos.

Este guia e que acompanha o passo a passo, vamos aprender a executar tarefas de aplicativo em segundo plano. Vamos abordar os principais conceitos e práticas recomendadas e, em seguida, passa pela criação de um aplicativo do mundo real que recebe atualizações de localização em segundo plano.

## <a name="contents"></a>Conteúdo

1.  [Introdução ao backgrounding no iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Demonstração do ciclo de vida do aplicativo](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [Técnicas de backgrounding do iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Passo a passo: backgrounding no iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [Orientação de backgrounding do iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Resumo

Neste guia, apresentamos as diferentes maneiras de fazer o processamento em segundo plano no iOS. Podemos coberto iOS estados do aplicativo e examinado a função backgrounding desempenha no ciclo de vida do aplicativo iOS. Além disso, aprendemos como podemos poderia registrar tarefas individuais ou aplicativos inteiros para operar em segundo plano no iOS. Por fim, podemos reforçado nossa compreensão de backgrounding no iOS com a criação de aplicativos que executam atualizações em segundo plano.



## <a name="related-links"></a>Links relacionados

- [Backgrounding no Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (amostra)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Local (exemplo)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Transferência simples em segundo plano (amostra)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [Execução em segundo plano do iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
