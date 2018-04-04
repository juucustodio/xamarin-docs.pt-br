---
title: Implementando com fragmentos
description: Android 3.0 apresentou fragmentos. Os fragmentos são componentes independentes e modulares usados para ajudar a solucionar a complexidade da produção de aplicativos que podem ser executados em telas de tamanhos diferentes. Este artigo explica como usar os fragmentos para desenvolver aplicativos xamarin e como dar suporte a fragmentos em 3.0 dispositivos previamente Android.
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 81f1f992de450ee62c4c1d2e80da858b024be594
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="implementing-with-fragments"></a>Implementando com fragmentos

_Android 3.0 apresentou fragmentos. Os fragmentos são componentes independentes e modulares usados para ajudar a solucionar a complexidade da produção de aplicativos que podem ser executados em telas de tamanhos diferentes. Este artigo explica como usar os fragmentos para desenvolver aplicativos xamarin e como dar suporte a fragmentos em 3.0 dispositivos previamente Android._


## <a name="overview"></a>Visão geral

Nesta seção, examinaremos como criar um aplicativo que será exibida uma lista de opções de Shakespeare e uma cotação de cada atividade selecionada. Nosso aplicativo utilizará os fragmentos de forma que podemos pode definir nossos componentes de interface do usuário em um único local, mas, em seguida, usá-los em diferentes fatores forma. Por exemplo, as capturas de tela a seguir mostram o aplicativo em execução em um tablet 10", bem como em um telefone:

[![Capturas de tela de exemplo de aplicativo em execução no tablet e telefone](images/intro-screenshot-sml.png)](images/intro-screenshot.png#lightbox)

Esta seção aborda os seguintes tópicos:

- **Criando fragmentos** &ndash; mostra como criar um fragmento para exibir uma lista de opções de Shakespeare e outro fragmento para exibir uma cotação de cada atividade.

- **Suporte a diferentes tamanhos de telas** &ndash; mostra como o aplicativo para aproveitar os maiores tamanhos de tela de layout.

- **Usando o pacote de suporte Android** &ndash; implementa o pacote de suporte Android e, em seguida, faz algumas pequenas alterações para as atividades no aplicativo, permitindo que ele seja executado em versões anteriores do Android.


## <a name="requirements"></a>Requisitos

Este passo a passo requer xamarin 4.0 ou superior. Também será necessário instalar o pacote de suporte Android, conforme descrito na documentação de fragmentos.


## <a name="introduction"></a>Introdução

No exemplo, criaremos nesta seção, as atividades não contém a lógica para carregar a lista, respondendo a seleção do usuário ou exibindo a citação para a ação selecionada. Essa lógica existe em fragmentos individuais.
Colocando essa lógica em fragmentos em si, podemos pode interromper o fluxo de trabalho do aplicativo para oferecer suporte a telas grandes com uma atividade ou telas pequenas com várias atividades sem precisar escrever uma lógica diferente para cada atividade. Em um tablet, ambos os fragmentos será em uma atividade. Em um telefone, os fragmentos serão hospedados em atividades diferentes.

Este aplicativo inclui as seguintes partes:

 **MainActivity** – exibe um ou ambos os fragmentos, dependendo do tamanho da tela. Essa é a atividade de inicialização.

 **TitlesFragment** – exibe uma lista de opções de Shakespeare na qual o usuário pode selecionar.

 **DetailsFragment** – exibe a cotação da ação selecionada.

 **DetailsActivity** – hospeda e exibe o DetailsFragment.
Esta atividade é usada pelos dispositivos com telas pequenas, como telefones.



## <a name="related-links"></a>Links relacionados

- [FragmentsWalkthrough (exemplo)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Visão geral do Designer](~/android/user-interface/android-designer/index.md)
- [Exemplos de xamarin: Honeycomb Galeria](https://developer.xamarin.com/samples/HoneycombGallery/)
- [Implementação de fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](http://developer.android.com/sdk/compatibility-library.html)
