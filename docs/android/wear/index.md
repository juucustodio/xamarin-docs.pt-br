---
title: Android Wear
description: Criando aplicativos para dispositivos Android portátil.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 67698ae7fe3ef9a7586d83e26ed276ba473396e5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457504"
---
# <a name="android-wear"></a>Android Wear

O desgaste do Android é uma versão do Android projetada para dispositivos portátil, como inspeções inteligentes. Esta seção inclui instruções sobre como instalar e configurar as ferramentas necessárias para o desenvolvimento de desgaste, um passo a passo para criar seu primeiro dispositivo de desgaste e uma lista de exemplos aos quais você pode se referir para criar seus próprios aplicativos de desgaste.

## <a name="getting-started"></a>[Introdução](~/android/wear/get-started/index.md)

Apresenta o desgaste do Android, descreve como instalar e configurar seu computador para desenvolvimento de desgaste e fornece etapas para ajudá-lo a criar e executar seu primeiro aplicativo de desgaste do Android em um emulador ou dispositivo de desgaste.

## <a name="user-interface"></a>[Interface do Usuário](~/android/wear/user-interface/index.md)

Explica os controles específicos de desgaste do Android e fornece links para exemplos que demonstram como usar esses controles.

## <a name="platform-features"></a>[Recursos da plataforma](~/android/wear/platform/index.md)

Os documentos nesta seção abrangem recursos específicos para o desgaste do Android. Aqui você encontrará um tópico que descreve como criar um WatchFace.

## <a name="screen-sizes"></a>[Tamanhos de tela](~/android/wear/screen-sizes.md)

Visualize e otimize sua interface do usuário para os tamanhos de tela disponíveis.

## <a name="deployment--testing"></a>[Implantação e Teste](~/android/wear/deploy-test/index.md)

Explica como implantar seu aplicativo de desgaste do Android em um dispositivo de desgaste do Android ou no emulador do Android configurado para desgaste. Ele também inclui dicas de depuração e informações sobre como configurar uma conexão Bluetooth entre o computador de desenvolvimento e um dispositivo Android.

## <a name="wear-apis"></a>[APIs de desgaste](https://developer.android.com/reference/android/support/wearable)

O site do desenvolvedor do Android fornece informações detalhadas sobre as principais APIs de desgaste, como [atividade de portátil](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intenções](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticação](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [complicações](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [renderização de complicações](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notificações](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [exibições](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).

## <a name="samples"></a>Exemplos

Você pode encontrar vários [exemplos](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) usando o desgaste do Android (ou vá diretamente para o [GitHub](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Amostra|Descrição|Captura de tela|
|--- |--- |--- |
|[SkeletonWear](/samples/xamarin/monodroid-samples/wear-skeletonwear)|Um exemplo simples dos conceitos básicos dos projetos portátil, incluindo GridViewPager e notificações interativas.|![Captura de tela de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub)|Uma demonstração simples do controle WatchViewStub que detecta a forma da tela e carrega automaticamente o layout correto. Veja como o WatchViewStub funciona no layout de **recursos/layout/main_activity.xml** .|![Captura de tela de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](/samples/xamarin/monodroid-samples/wear-recipeassistant)|Demonstração de páginas de notificação de desgaste, na forma de etapas da receita. As notificações são criadas no RecipeService.cs.|![Captura de tela de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](/samples/xamarin/monodroid-samples/wear-elizachat)|Exemplo divertido de interação com um "assistente pessoal" chamado Eliza, usando notificações interativas de desgaste para criar uma conversa usando respostas gravadas.|![Captura de tela de ElizaChat](images/eliza.png)|
|[GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager implementa o padrão de navegação 2D, no qual o usuário passa verticalmente e horizontalmente para navegar pelas opções e conteúdo.|![Captura de tela de GridViewPager](images/gridviewpager.png)|
|[WatchFace](/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace é uma face de inspeção personalizada com as mãos de hora, minuto e segundo do estilo analógico. Este exemplo demonstra como criar um serviço Watch facial que desenha a hora atual e lida com o modo de ambiente e os eventos de alteração de visibilidade. Ele inclui um receptor de difusão que escuta as alterações de fuso horário e atualiza automaticamente o tempo de acordo.|![Captura de tela de WatchFace](images/gridviewpager.png)|

## <a name="videos"></a>vídeos

Confira estes links de vídeo que discutem o Xamarin. Android com suporte de desgaste:

|Descrição|Captura de tela|
|--- |--- |
|[Android L e muito mais](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; O Android L Developer Preview introduziu uma infinidade de novas APIs para que os desenvolvedores tirem proveito do, incluindo design de material, notificações e novas animações, para citar alguns.|![Captura de tela de vídeo da apresentação](images/video-android-l.png)|
|[O C# está em meus ouvidos e nos meus olhos: o Google Glass e o desgaste](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; do Android A computação portátil pode parecer algo do futuro (ou de um episódio de gadget de Inspetor), mas muitas pessoas já estão adotando o futuro hoje! Os desenvolvedores de C# sabem disso e já têm as ferramentas e habilidades para aproveitar o poder dos dispositivos portátil (de evoluir 2014).|![Captura de tela de vídeo da apresentação](images/video-eyes-ears.png)|
|[O que há de novo no Xamarin. Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L, Android desgaste, TV Android, Android, design de material e arte; o que isso significa para você como desenvolvedor do Xamarin? de evoluir 2014.|![Captura de tela de vídeo da apresentação](Images/video-whats-new.png)|

<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->