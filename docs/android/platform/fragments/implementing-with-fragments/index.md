---
title: Implementando fragmentos - passo a passo
description: Este artigo explica como usar os fragmentos para desenvolver aplicativos xamarin.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 92c68298d7abd2570efd89e12d7cfb6364e90972
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="implementing-fragments---walkthrough"></a>Implementando fragmentos - passo a passo

_Os fragmentos são componentes independentes e modulares, que podem ajudar a resolver a complexidade de aplicativos Android que dispositivos com uma variedade de tamanhos de tela de destino. Este artigo explica como criar e usar fragmentos ao desenvolver aplicativos xamarin._

## <a name="overview"></a>Visão geral

Nesta seção, você verá como criar e usar os fragmentos em um aplicativo xamarin. Este aplicativo exibirá os títulos de várias ações por William Shakespeare em uma lista. Quando o usuário toca no título de um jogo, o aplicativo exibirá uma citação do que são executados em uma atividade separada:

[![Aplicativo em execução em um telefone Android no modo retrato](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando o telefone for girado para modo paisagem, alterará a aparência do aplicativo: lista de opções e aspas aparecerá na mesma atividade. Quando uma opção é selecionada, as aspas serão exibidos na mesma atividade:

[![Aplicativo em execução em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Por fim, se o aplicativo estiver em execução em um tablet:

[![Aplicativo em execução em um tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Este aplicativo de exemplo pode adaptar facilmente a diferentes fatores forma e orientações com alterações mínimas de código usando fragmentos e [Layouts alternativos](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Os dados para o aplicativo continuará a existir em duas matrizes de cadeia de caracteres que são embutidos em código no aplicativo como matrizes de cadeia de caracteres do c#. Cada uma das matrizes servirá como a fonte de dados para um fragmento.  Uma matriz conterá o nome de alguns recursos por Shakespeare e outra matriz conterá uma citação do que são executados. Quando o aplicativo é iniciado, ele exibirá os nomes de reprodução em um `ListFragment`. Quando o usuário clica em um jogo de `ListFragment`, o aplicativo é iniciado outra atividade que exibirá a cotação.

A interface do usuário para o aplicativo consistirá em dois layouts, um para retrato e outro para o modo de paisagem. Em tempo de execução Android determinará qual layout para carregar com base na orientação do dispositivo e fornecerá um que o layout para a atividade para processar. Toda a lógica para responder a cliques do usuário e exibir os dados estarão contidas em fragmentos. As atividades no aplicativo existem apenas como contêineres que hospedarão os fragmentos.

Este passo a passo será dividida em duas guias. O [primeira parte](./walkthrough.md) vai se concentrar nas partes principais do aplicativo. Um único conjunto de layouts (otimizado para o modo de retrato) será criado, junto com dois fragmentos e duas atividades:

1. `MainActivity` &nbsp; Essa é a atividade de inicialização para o aplicativo.
1. `TitlesFragment` &nbsp; Este fragmento exibirá uma lista de títulos de recursos que foram gravados por William Shakespeare. Ele será hospedado por `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` iniciará o `PlayQuoteActivity` em resposta ao usuário selecionar uma opção em `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Este fragmento exibirá uma cotação de uma opção por William Shakespeare. Ele será hospedado por `PlayQuoteActivity`.

O [segunda parte deste passo a passo](./walkthrough-landscape.md) discutir adicionando um layout alternativo (otimizado para o modo de paisagem), que exibe ambos os fragmentos na tela. Além disso, algumas pequenas alterações de código serão feitas para o código de forma que o aplicativo adaptará seu comportamento para o número de fragmentos que simultaneamente são exibidos na tela.

## <a name="related-links"></a>Links relacionados

- [FragmentsWalkthrough (exemplo)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Visão geral do Designer](~/android/user-interface/android-designer/index.md)
- [Implementação de fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](http://developer.android.com/sdk/compatibility-library.html)
