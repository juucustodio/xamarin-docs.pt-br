---
title: Android Wear
description: Criando aplicativos para Android dispositivos portáteis.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: 75b78f27fd6b3a726de9075ce36364168401ec60
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171463"
---
# <a name="android-wear"></a>Android Wear

Android Wear é uma versão do Android que foi projetado para dispositivos portáteis, como relógios inteligentes. Esta seção inclui instruções sobre como instalar e configurar as ferramentas necessárias para desenvolvimento de desgaste, passo a passo para criar seu primeiro dispositivo de desgaste e uma lista de exemplos que você pode consultar para a criação de seus próprios aplicativos de uso.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introdução](~/android/wear/get-started/index.md)

Apresenta o Android Wear, descreve como instalar e configurar seu computador para o desenvolvimento de desgaste e fornece etapas para ajudá-lo a criar e executar seu primeiro aplicativo Android Wear em um emulador ou dispositivo Wear.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interface do Usuário](~/android/wear/user-interface/index.md)

Explica o Android Wear específicas controles e fornece links para exemplos que demonstram como usar esses controles.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Recursos da plataforma](~/android/wear/platform/index.md)

Documentos nesta seção abordam os recursos específicos do Android Wear. Aqui você encontrará um tópico que descreve como criar um WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tamanhos de tela](~/android/wear/screen-sizes.md)

Visualize e otimizar sua interface do usuário para os tamanhos de tela disponíveis.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Implantação e Teste](~/android/wear/deploy-test/index.md)

Explica como implantar seu aplicativo Android Wear para um dispositivo Android Wear ou configurado para o desgaste de emulador Android. Ele também inclui dicas e fornece informações sobre como configurar uma conexão Bluetooth entre o computador de desenvolvimento e um dispositivo Android de depuração.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[APIs de uso](https://developer.android.com/reference/android/support/wearable)

O site de desenvolvedor do Android fornece informações detalhadas sobre principais APIs de desgaste, como [atividade portáteis](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intenções](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticação](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complicações](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [complicações de renderização](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notificações](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [exibições](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Exemplos

Você pode encontrar um número de [amostras](https://developer.xamarin.com/samples/android/Android%20Wear/) usando o Android Wear (ou vá diretamente para [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

|Amostra|Descrição|Captura de tela|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|Um exemplo simples dos conceitos básicos de projetos, portáteis, incluindo notificações interativas e GridViewPager.|![Captura de tela de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|Uma demonstração simple do controle WatchViewStub que detecta a forma de tela e carrega automaticamente o layout correto.  Veja como WatchViewStub funciona **Resources/layout/main_activity.xml** layout.|![Captura de tela de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|Demonstração de páginas de notificação de desgaste, na forma de etapas de receita. As notificações são criadas no RecipeService.cs.|![Captura de tela de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|Exemplo divertido de interagir com um assistente"pessoal" chamado Eliza, usando notificações de desgaste interativas para criar uma conversa usando respostas comprimidas.|![Captura de tela de ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|GridViewPager implementa o padrão de navegação 2D, em que o usuário passa o dedo verticalmente e horizontalmente para navegar por meio de opções e conteúdo.|![Captura de tela de GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace é uma Mostrador personalizado com estilo analógico hora, minuto e segundo mãos. Este exemplo demonstra como criar um serviço de detecção facial watch que desenha a hora atual e a visibilidade e o modo de ambiente de identificadores de eventos de alteração. Ele inclui um receptor de difusão que escuta de alterações de fuso horário e atualiza automaticamente o tempo de acordo.|![Captura de tela de WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Vídeos

Confira o vídeo dar suporte a links que abordam o xamarin. Android Wear com o seguinte:

|Descrição|Captura de tela|
|--- |--- |
|[Android L e muito mais](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; L Developer Preview o Android introduziu uma grande quantidade de novas APIs para os desenvolvedores tirem proveito, incluindo Design de Material, notificações e novas animações, para citar alguns.|![Captura de tela de vídeo da apresentação](images/video-android-l.png)|
|[C#é em meu ouvidos em meus olhos: Google Glass e o Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; portáteis de computação pode parecer algo do futuro (ou um episódio de Gadget de Inspetor), mas muitas pessoas já estão adotando o futuro hoje mesmo! C#os desenvolvedores sabem disso e já tem as ferramentas e habilidades para aproveitar a capacidade dos dispositivos portáteis (a partir de 2014 evoluem).|![Captura de tela de vídeo da apresentação](images/video-eyes-ears.png)|
|[O que há de novo no xamarin. Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, Android Wear, TV Android, Android Auto, Design de Material e arte; o que faz isso significa para você como um desenvolvedor de Xamarin? do 2014 evoluem.|![Captura de tela de vídeo da apresentação](Images/video-whats-new.png)|


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
