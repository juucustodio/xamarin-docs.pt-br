---
title: Implementando Fragmentos - Passo a passo
description: Este artigo mostra como usar fragmentos para desenvolver aplicativos Xamarin.Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027414"
---
# <a name="implementing-fragments---walkthrough"></a>Implementação de fragmentos - passo a passo

_Os fragmentos são componentes modulares independentes que podem ajudar a lidar com a complexidade dos aplicativos Android que visam dispositivos com uma variedade de tamanhos de tela. Este artigo mostra como criar e usar fragmentos ao desenvolver aplicativos Xamarin.Android._

## <a name="overview"></a>Visão geral

Nesta seção, você vai percorrer como criar e usar fragmentos em um aplicativo Xamarin.Android. Este aplicativo exibirá os títulos de várias peças de William Shakespeare em uma lista. Quando o usuário tocar no título de uma reprodução, o aplicativo exibirá uma citação dessa reprodução em uma atividade separada:

[![Aplicativo rodando em um telefone Android em modo retrato](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando o telefone é girado para o modo paisagem, a aparência do aplicativo mudará: tanto a lista de reproduzidos quanto as citações aparecerão na mesma atividade. Quando uma reprodução é selecionada, a citação será exibida na mesma atividade:

[![Aplicativo rodando em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Finalmente, se o aplicativo estiver sendo executado em um tablet:

[![Aplicativo rodando em um tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Esta aplicação de amostra pode facilmente adaptar-se aos diferentes fatores de forma e orientações com alterações mínimas de código usando fragmentos e [layouts alternativos](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Os dados para o aplicativo existirão em dois arrays de string que são codificados no aplicativo como matrizes de stringc#. Cada uma das matrizes servirá como fonte de dados para um fragmento.  Uma matriz vai segurar o nome de algumas peças de Shakespeare, e a outra matriz vai segurar uma citação dessa peça. Quando o aplicativo for iniciado, ele exibirá os nomes de reprodução em um `ListFragment`. Quando o usuário clica em `ListFragment`uma reprodução no , o aplicativo iniciará outra atividade que exibirá a cotação.

A interface do usuário para o aplicativo será composta por dois layouts, um para retrato e outro para o modo paisagem. Em tempo de execução, o Android determinará qual layout carregar com base na orientação do dispositivo e fornecerá esse layout para a Atividade renderizar. Toda a lógica para responder aos cliques do usuário e exibir os dados será contida em fragmentos. As atividades no aplicativo existem apenas como contêineres que hospedarão os fragmentos.

Este passo a passo será dividido em dois guias. A [primeira parte](./walkthrough.md) se concentrará nas partes centrais da aplicação. Um único conjunto de layouts (otimizado para o modo retrato) será criado, juntamente com dois fragmentos e duas atividades:

1. `MainActivity`&nbsp; Esta é a atividade de inicialização para o aplicativo.
1. `TitlesFragment`&nbsp; Este fragmento exibirá uma lista de títulos de peças que foram escritas por William Shakespeare. Será hospedado por `MainActivity`.
1. `PlayQuoteActivity`iniciará `PlayQuoteActivity` a resposta ao usuário selecionando uma reprodução em `TitlesFragment`. &nbsp; `TitlesFragment`
1. `PlayQuoteFragment`&nbsp; Este fragmento mostrará uma citação de uma peça de William Shakespeare. Será hospedado por `PlayQuoteActivity`.

A [segunda parte deste passo a passo](./walkthrough-landscape.md) discutirá a adição de um layout alternativo (otimizado para o modo paisagem) que exibirá ambos os fragmentos na tela. Além disso, algumas pequenas alterações de código serão feitas no código para que o aplicativo adapte seu comportamento ao número de fragmentos que são exibidos simultaneamente na tela.

## <a name="related-links"></a>Links relacionados

- [FragmentosWalkthrough (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Visão geral do designer](~/android/user-interface/android-designer/index.md)
- [Implementando Fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](https://developer.android.com/sdk/compatibility-library.html)
