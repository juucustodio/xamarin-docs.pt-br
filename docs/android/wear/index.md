---
title: Android Wear
description: Criando aplicativos para dispositivos Android de vestir.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/16/2018
ms.openlocfilehash: 31114df0b631aea909e82f3a8b836d5ef922d2c1
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2018
---
# <a name="android-wear"></a>Android Wear

Desgaste Android é uma versão do Android que foi projetado para dispositivos vestir como relógios. Esta seção inclui instruções sobre como instalar e configurar as ferramentas necessárias para o desenvolvimento de desgaste, passo a passo para criar seu primeiro dispositivo de desgaste e uma lista de exemplos que você pode consultar para criar seus próprios aplicativos de uso.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introdução](~/android/wear/get-started/index.md)

Apresenta desgaste Android, descreve como instalar e configurar o computador para o desenvolvimento de desgaste e fornece etapas para ajudá-lo a criar e executar seu primeiro aplicativo do Android desempenham em um emulador ou dispositivo de desgaste.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interface do Usuário](~/android/wear/user-interface/index.md)

Explica o Android desgaste controla e fornece links para exemplos que demonstram como usar esses controles.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Recursos da plataforma](~/android/wear/platform/index.md)

Documentos nesta seção abordam os recursos específicos do desgaste Android. Aqui você encontrará um tópico que descreve como criar um WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tamanhos de tela](~/android/wear/screen-sizes.md)

Visualize e otimizar sua interface de usuário para os tamanhos de tela disponíveis.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Implantação e Teste](~/android/wear/deploy-test/index.md)

Explica como implantar seu aplicativo Android usam a um dispositivo Android desgaste ou emulador Android configurado para uso. Também inclui informações sobre como configurar uma conexão Bluetooth entre o computador de desenvolvimento e um dispositivo Android e dicas de depuração.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Use APIs](https://developer.android.com/reference/android/support/wearable)

O site do desenvolvedor Android fornece informações detalhadas sobre APIs de uso de chave como [atividade vestir](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [tentativas](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticação](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complicações](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [complicações renderização](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notificações](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [exibições](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Exemplos

Você pode encontrar um número de [exemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) usando desgaste Android (ou vá diretamente para [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

|Amostra|Descrição|captura de tela|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|Um exemplo simples de Noções básicas de vestir projetos, incluindo GridViewPager e notificações interativas.|![Captura de tela de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|Uma demonstração simple do controle WatchViewStub detectar forma de tela e carrega automaticamente o layout correto.  Consulte como WatchViewStub funciona no **Resources/layout/main_actvity.xml** layout.|![Captura de tela de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|Demonstração de desgaste páginas de notificação, na forma de etapas de receita. As notificações são criadas no RecipeService.cs.|![Captura de tela de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|Exemplo divertido de interagir com um assistente"pessoal" chamado Eliza, usando notificações de desgaste interativas para criar uma conversa usando respostas e padronizadas.|![Captura de tela de ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|GridViewPager implementa o padrão 2D de navegação, em que o usuário obtém verticalmente e horizontalmente para percorrer as opções e conteúdo.|![Captura de tela de GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace é uma face do relógio de personalizado com estilo analógico hora, minuto e segundo mãos. Este exemplo demonstra como criar um serviço de face de observação que desenha a hora atual e eventos de alteração de modo de ambiente de identificadores e visibilidade. Ele inclui um receptor de difusão que escuta de alterações de fuso horário e atualiza automaticamente o tempo.|![Captura de tela de WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Vídeos

Check-out de vídeo esses links que abordam xamarin com desgaste de suporte:

|Descrição|captura de tela|
|--- |--- |
|[Android L e muito mais](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; o Android Developer Preview L introduziu uma infinidade de novas APIs para desenvolvedores aproveitar, incluindo o Material de Design, notificações e novas animações, para citar alguns.|![Captura de tela de vídeo de apresentação](images/video-android-l.png)|
|[C# é em meu ouvidos em meus olhos: Google Glass e desgaste Android](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; computação vestir pode parecer algo do futuro (ou um episódio de Inspetor Gadget), mas muitas pessoas já estão adotando futuro hoje! Os desenvolvedores do c# sabem isso e já tem as ferramentas e habilidades aproveitamento da capacidade de vestir dispositivos (Evolve 2014).|![Captura de tela de vídeo de apresentação](images/video-eyes-ears.png)|
|[O que há de novo no xamarin](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, desgaste Android, TV Android, Auto Android, Material de Design e arte; o que faz isso médio para você como um desenvolvedor Xamarin? do Evolve 2014.|![Captura de tela de vídeo de apresentação](Images/video-whats-new.png)|


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
