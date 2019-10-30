---
title: Android Wear
description: Criando aplicativos para dispositivos Android portátil.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 63fb9c3b1484026cb390a8a475c711562dfd0771
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028584"
---
# <a name="android-wear"></a>Android Wear

O desgaste do Android é uma versão do Android projetada para dispositivos portátil, como inspeções inteligentes. Esta seção inclui instruções sobre como instalar e configurar as ferramentas necessárias para o desenvolvimento de desgaste, um passo a passo para criar seu primeiro dispositivo de desgaste e uma lista de exemplos aos quais você pode se referir para criar seus próprios aplicativos de desgaste.

## <a name="getting-startedandroidwearget-startedindexmd"></a>[Introdução](~/android/wear/get-started/index.md)

Apresenta o desgaste do Android, descreve como instalar e configurar seu computador para desenvolvimento de desgaste e fornece etapas para ajudá-lo a criar e executar seu primeiro aplicativo de desgaste do Android em um emulador ou dispositivo de desgaste.

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interface do Usuário](~/android/wear/user-interface/index.md)

Explica os controles específicos de desgaste do Android e fornece links para exemplos que demonstram como usar esses controles.

## <a name="platform-featuresandroidwearplatformindexmd"></a>[Recursos da plataforma](~/android/wear/platform/index.md)

Os documentos nesta seção abrangem recursos específicos para o desgaste do Android. Aqui você encontrará um tópico que descreve como criar um WatchFace.

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tamanhos de tela](~/android/wear/screen-sizes.md)

Visualize e otimize sua interface do usuário para os tamanhos de tela disponíveis.

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Implantação e Teste](~/android/wear/deploy-test/index.md)

Explica como implantar seu aplicativo de desgaste do Android em um dispositivo de desgaste do Android ou no emulador do Android configurado para desgaste. Ele também inclui dicas de depuração e informações sobre como configurar uma conexão Bluetooth entre o computador de desenvolvimento e um dispositivo Android.

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[APIs de desgaste](https://developer.android.com/reference/android/support/wearable)

O site do desenvolvedor do Android fornece informações detalhadas sobre as principais APIs de desgaste, como [atividade de portátil](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intenções](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticação](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [complicações](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [renderização de complicações](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notificações](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [ Exibições](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).

## <a name="samples"></a>Exemplos

Você pode encontrar vários [exemplos](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear) usando o desgaste do Android (ou vá diretamente para o [GitHub](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Amostra|Descrição|Captura|
|--- |--- |--- |
|[SkeletonWear](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)|Um exemplo simples dos conceitos básicos dos projetos portátil, incluindo GridViewPager e notificações interativas.|![Captura de tela de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub)|Uma demonstração simples do controle WatchViewStub que detecta a forma da tela e carrega automaticamente o layout correto. Veja como o WatchViewStub funciona no layout de **recursos/layout/main_activity. xml** .|![Captura de tela de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant)|Demonstração de páginas de notificação de desgaste, na forma de etapas da receita. As notificações são criadas no RecipeService.cs.|![Captura de tela de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-elizachat)|Exemplo divertido de interação com um "assistente pessoal" chamado Eliza, usando notificações interativas de desgaste para criar uma conversa usando respostas gravadas.|![Captura de tela de ElizaChat](images/eliza.png)|
|[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager implementa o padrão de navegação 2D, no qual o usuário passa verticalmente e horizontalmente para navegar pelas opções e conteúdo.|![Captura de tela de GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace é uma face de inspeção personalizada com as mãos de hora, minuto e segundo do estilo analógico. Este exemplo demonstra como criar um serviço Watch facial que desenha a hora atual e lida com o modo de ambiente e os eventos de alteração de visibilidade. Ele inclui um receptor de difusão que escuta as alterações de fuso horário e atualiza automaticamente o tempo de acordo.|![Captura de tela de WatchFace](images/gridviewpager.png)|

## <a name="videos"></a>Vídeos

Confira estes links de vídeo que discutem o Xamarin. Android com suporte de desgaste:

|Descrição|Captura|
|--- |--- |
|O [Android l e muito mais](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; o Android l Developer Preview introduziu uma infinidade de novas APIs para que os desenvolvedores tirem proveito do, incluindo design de material, notificações e novas animações, para citar alguns.|![Captura de tela de vídeo da apresentação](images/video-android-l.png)|
|[está em meus ouvidos e nos meus olhos: o Google Glass e o desgaste do Android&ndash;computação portátil pode parecer algo do futuro (ou um episódio do gadget do Inspetor), mas muitas pessoas já estão adotando o futuro hoje! C# ](https://www.youtube.com/watch?v=80H8tXByZQc) C#os desenvolvedores sabem disso e já têm as ferramentas e habilidades para aproveitar o poder dos dispositivos portátil (de evoluir 2014).|![Captura de tela de vídeo da apresentação](images/video-eyes-ears.png)|
|[O que há de novo no Xamarin. android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L, Android desgaste, Android TV, Android auto, design de material e arte; o que isso significa para você como desenvolvedor do Xamarin? de evoluir 2014.|![Captura de tela de vídeo da apresentação](Images/video-whats-new.png)|

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
