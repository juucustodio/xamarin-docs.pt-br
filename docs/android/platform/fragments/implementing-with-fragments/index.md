---
title: Implementação de fragmentos - instruções passo a passo
description: Este artigo explica como usar os fragmentos para desenvolver aplicativos xamarin. Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 2ff4729e68497391d41521da26917571c146b541
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953252"
---
# <a name="implementing-fragments---walkthrough"></a>Implementação de fragmentos - instruções passo a passo

_Os fragmentos são componentes independentes e modulares que podem ajudar a lidar com a complexidade de aplicativos Android destinados a dispositivos com uma variedade de tamanhos de tela. Este artigo explica como criar e usar fragmentos ao desenvolver aplicativos xamarin. Android._

## <a name="overview"></a>Visão geral

Nesta seção, você verá como criar e usar os fragmentos em um aplicativo xamarin. Android. Este aplicativo exibirá os títulos das várias ações por William Shakespeare em uma lista. Quando o usuário toca no título de um jogo, o aplicativo exibirá uma citação do que são executados em uma atividade separada:

[![Aplicativo em execução em um telefone Android no modo retrato](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando o telefone for girado para modo paisagem, alterará a aparência do aplicativo: lista de desempenha e aspas será exibido na mesma atividade. Quando uma opção é selecionada, a citação será a exibição na mesma atividade:

[![Aplicativo em execução em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Por fim, se o aplicativo estiver em execução em um tablet:

[![Aplicativo em execução em um tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Este aplicativo de exemplo pode adaptar facilmente a diferentes fatores forma e orientações com alterações mínimas de código por meio de fragmentos e [Layouts alternativos](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Os dados para o aplicativo continuará a existir em duas matrizes de cadeia de caracteres que são codificadas no aplicativo como C# matrizes de cadeia de caracteres. Cada uma das matrizes servirá como a fonte de dados para um fragmento.  Uma matriz conterá o nome de alguns recursos por Shakespeare e outra matriz conterá uma citação do que são executadas. Quando o aplicativo é iniciado, ele exibirá os nomes de reprodução em um `ListFragment`. Quando o usuário clica em um play no `ListFragment`, o aplicativo iniciará outra atividade que exibirá a citação.

A interface do usuário para o aplicativo consistirá em dois layouts, um para retrato e outro para modo paisagem. Em tempo de execução, o Android determinará qual layout para carregar com base na orientação do dispositivo e fornecerá o layout para a atividade para renderizar. Toda a lógica para responder a cliques do usuário e exibição de dados estará contida em fragmentos. As atividades no aplicativo existem apenas como contêineres que hospedarão os fragmentos.

Este passo a passo será ser dividido em duas guias. O [primeira parte](./walkthrough.md) se concentrará em partes principais do aplicativo. Um único conjunto de layouts (otimizado para o modo retrato) será criado, junto com dois fragmentos e duas atividades:

1. `MainActivity` &nbsp; Essa é a atividade de inicialização para o aplicativo.
1. `TitlesFragment` &nbsp; Este fragmento exibirá uma lista de títulos de recursos que foram escritos por William Shakespeare. Ele será hospedado por `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` iniciará a `PlayQuoteActivity` em resposta ao usuário selecionar uma opção no `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Este fragmento exibirá uma citação de um play por William Shakespeare. Ele será hospedado por `PlayQuoteActivity`.

O [segunda parte deste passo a passo](./walkthrough-landscape.md) discutirá a adição de um layout alternativo (otimizado para modo paisagem), que exibe os dois fragmentos na tela. Além disso, algumas pequenas alterações de código serão feitas no código, de modo que o aplicativo se adaptará seu comportamento para o número de fragmentos que são exibidas simultaneamente na tela.

## <a name="related-links"></a>Links relacionados

- [FragmentsWalkthrough (amostra)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Visão geral do Designer](~/android/user-interface/android-designer/index.md)
- [Implementação de fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](https://developer.android.com/sdk/compatibility-library.html)
