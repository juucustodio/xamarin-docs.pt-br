---
title: Introdução ao Android Wear
description: Com a introdução do Android Wear do Google, você não está restrito a apenas os telefones e tablets quando se trata de desenvolvimento de ótimos aplicativos Android. Suporte do xamarin. Android para o Android Wear possibilita que você execute C# código em sua mão! Esta Introdução fornece uma visão geral básica do Android Wear, descreve seus principais recursos e oferece uma visão geral dos recursos disponíveis no 2.0 Android Wear. Ele lista alguns dos dispositivos mais populares do Android Wear e fornece links para documentação do Google Android Wear essencial para ler mais.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a35cb82f4f6d20e91f45a782c73d3ef811947c3a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117779"
---
# <a name="introduction-to-android-wear"></a>Introdução ao Android Wear

_Com a introdução do Android Wear do Google, você não está restrito a apenas os telefones e tablets quando se trata de desenvolvimento de ótimos aplicativos Android. Suporte do xamarin. Android para o Android Wear possibilita que você execute C# código em sua mão! Esta Introdução fornece uma visão geral básica do Android Wear, descreve seus principais recursos e oferece uma visão geral dos recursos disponíveis no 2.0 Android Wear. Ele lista alguns dos dispositivos mais populares do Android Wear e fornece links para documentação do Google Android Wear essencial para ler mais._


## <a name="overview"></a>Visão geral

Android Wear é executado em uma variedade de dispositivos, incluindo a primeira geração 360 Motorola, inspeção de variáveis do LG G e a engrenagem Samsung ao vivo. Uma segunda geração, incluindo 3 de SmartWatch da Sony, também foi lançada com funcionalidades adicionais, incluindo GPS internos e reprodução de música offline. Para o Android Wear 2.0, Google se juntou à LG para duas novas inspeções: de esporte de inspeção LG e o estilo de inspeção LG.

![Dispositivos com Android Wear 2.0](intro-to-wear-images/hero-image.png "exemplo Android Wear 2.0 dispositivos")

Suporte de xamarin. Android 5.0 e posterior dá suporte ao Android Wear por meio de nosso 4.4W Android (API 20) e controles de interface do usuário específica de desgaste de um pacote do NuGet que adiciona adicionais. Xamarin. Android 5.0 e posterior também inclui a funcionalidade para empacotar seus aplicativos de desgaste. Pacotes do NuGet também estão disponíveis para Android 2.0 desgaste conforme descrito mais adiante neste guia.


## <a name="android-wear-basics"></a>Noções básicas sobre o Android Wear

Android Wear tem um paradigma de interface do usuário que é diferente do Android aplicativos portáteis. A primeira onda de desgaste aplicativos foram projetados para estender um complemento aplicativo portátil em alguma forma, mas a partir do Android Wear 2.0, apps desgaste pode ser usado de forma autônoma. Quando você implanta um aplicativo de desgaste, ele é empacotado com um aplicativo portátil de complementar. Porque a maioria desgaste aplicativos dependem de um aplicativo portátil complementar, precisa de alguma maneira de se comunicar com aplicativos portáteis. As seções a seguir descrevem esses cenários de uso e descrevem os recursos essenciais do Android Wear. 



### <a name="usage-scenarios"></a>Cenários de uso

A primeira versão do Android Wear estava concentrada principalmente em Estendendo aplicativos portáteis atuais com notificações aprimorados e sincronizar dados entre o aplicativo portátil e o aplicativo portáteis. Portanto, esses cenários são relativamente simples de implementar.


#### <a name="wearable-notifications"></a>Notificações portáteis

É a maneira mais simples para dar suporte ao Android Wear aproveitar a natureza compartilhada notificações entre o portátil e o dispositivo portátil. Usando a API de notificação v4 suporte e o `WearableExtender` classe (disponível na [biblioteca de suporte de Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), você pode aproveitar os recursos nativos da plataforma, como cartões de estilo de caixa de entrada ou entrada de voz. O [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) exemplo fornece código de exemplo que demonstra como enviar uma lista de notificações para um dispositivo Android Wear. 



#### <a name="companion-applications"></a>Aplicativos complementares

Outra estratégia é criar um aplicativo completo que é executado nativamente no dispositivo portáteis e pares com um aplicativo portátil de complementar. Um bom exemplo dessa abordagem é a [teste](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) aplicativo de exemplo que demonstra como criar um teste que é executado em um dispositivo de mão e pergunta perguntas do teste no dispositivo portáteis. 



### <a name="user-interface"></a>Interface do Usuário

O padrão de navegação principal para desgaste é uma série de cartões organizadas verticalmente. Cada uma dessas fichas pode ter ações associadas que são colocadas em camadas-out na mesma linha. O `GridViewPager` classe fornece essa funcionalidade, ele segue o mesmo conceito do adaptador `ListView`. Normalmente, você associar o `GridViewPager` com um `FragmentGridPagerAdaptor` (ou `GridPagerAdaptor`) que lhe permite representar cada linha e coluna de células um `Fragment`: 

[![Navegação de desgaste](intro-to-wear-images/2d-picker-sml.png "Wear navegação")](intro-to-wear-images/2d-picker.png#lightbox)

Desgaste também faz uso dos botões de ação que consistem em uma grande colorido círculo com texto de descrição pequeno abaixo dela (conforme ilustrado acima).  O [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) exemplo demonstra como usar `GridViewPager` e `GridPagerAdapter` em um aplicativo de desgaste.

2.0 Android Wear adiciona uma gaveta de navegação, uma gaveta de ação e botões de ação embutida para a interface do usuário de desgaste. Para obter mais informações sobre os elementos de interface do usuário 2.0 Android Wear, consulte o Android [anatomia](https://www.google.com/design/spec-wear/system-overview/anatomy.html) tópico. 



### <a name="communications"></a>Comunicações

Android Wear fornece duas APIs para facilitar a comunicação entre aplicativos portáteis e aplicativos portáteis complementares de comunicação diferentes: 

**API de dados** &ndash; essa API é semelhante a um repositório de dados sincronizados entre o dispositivo portátil e o dispositivo de mão. Android cuida de propagar alterações entre portáteis e portáteis quando ele é ideal para fazer isso. Quando o dispositivo portátil está fora do intervalo, filas de sincronização para um momento posterior. O ponto de entrada principal para essa API é `WearableClass.DataApi`. Para obter mais informações sobre essa API, consulte o Android [itens de sincronização de dados](https://developer.android.com/training/wearables/data-layer/data-items.html) tópico. 

**API de mensagem** &ndash; essa API possibilita que você use um caminho de comunicação de nível inferior: uma pequena carga é enviada unidirecional sem sincronização entre os aplicativos portáteis e portáteis.
O ponto de entrada principal para essa API é `WearableClass.MessageApi`.
Para obter mais informações sobre essa API, consulte o Android [enviando e recebendo mensagens](https://developer.android.com/training/wearables/data-layer/messages.html) tópico.

Você pode optar por registrar retornos de chamada para receber essas mensagens por meio de cada uma das interfaces de ouvinte de API ou, Alternativamente, implementar um serviço em seu aplicativo que deriva de `WearableListenerService`.
Este serviço será automaticamente instanciado pelo Android Wear.
O [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) exemplo ilustra como implementar um `WearableListenerService`.



### <a name="deployment"></a>Implantação

Cada aplicativo portáteis é implantado com seu próprio arquivo APK inserido dentro do aplicativo principal APK. Esse pacote é tratado automaticamente no xamarin. Android 5.0 e posterior, mas deve ser executada manualmente para versões do xamarin. Android anteriores à versão 5.0. 
[Trabalhando com o empacotamento](~/android/wear/deploy-test/packaging.md) explica a implantação em mais detalhes. 



## <a name="going-further"></a>Indo mais além 

É a melhor maneira de se familiarizar com o Android Wear compilar e testar seu primeiro aplicativo. A lista a seguir fornece uma ordem de leitura recomendada para ajudá-lo a se familiarizar rapidamente:

1.  [Configuração e instalação](~/android/wear/get-started/installation.md) fornece instruções detalhadas para instalar e configurar seu ambiente de desenvolvimento para criação de aplicativos xamarin. Android Wear. 

2.  Depois que você instalou os pacotes necessários e configurou um dispositivo ou emulador, consulte [Olá, Wear](~/android/wear/get-started/hello-wear.md) para obter instruções passo a passo que explicam como criar um projeto do Android Wear pequeno botão identificadores clica e exibe um Clique em contador no dispositivo Wear. 

3.  [Implantação e teste](~/android/wear/deploy-test/index.md) fornece informações mais detalhadas sobre como configurar e implantar a emuladores e dispositivos, incluindo instruções sobre como implantar seu aplicativo em um dispositivo Wear via Bluetooth.

4.  [Trabalhando com tamanhos de tela](~/android/wear/screen-sizes.md) explica como visualizar e otimizar sua interface do usuário para diversos tamanhos de tela disponível em dispositivos de desgaste. 

5.  [Trabalhando com o empacotamento](~/android/wear/deploy-test/packaging.md) descreve as etapas de empacotamento manual de desgaste aplicativos para distribuição no Google Play.

Depois de criar seu primeiro aplicativo de desgaste, convém tentar criar uma Mostrador personalizado para o Android Wear. 
[Criar uma Mostrador](~/android/wear/platform/creating-a-watchface.md) fornece código de exemplo e instruções passo a passo para desenvolver um stripped interromper serviço de detecção facial watch digital, seguido por mais de código que aprimora a ele um mostrador analógico-style com recursos adicionais. 



## <a name="android-wear-20"></a>Android Wear 2.0

2.0 Android Wear apresenta uma variedade de novos recursos e funcionalidades, como *complicações*, layouts, gavetas de navegação e a ação e notificações expandidas em curva. Além disso, o desgaste 2.0 possibilita para criar aplicativos autônomos que trabalhar independentemente dos aplicativos portáteis. O novo *gestos de pulso* capacidade permite interações com uma só mão com seu aplicativo. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar com a usá-las em seu aplicativo.



### <a name="install-wear-20-packages"></a>Instalação Wear 2.0 pacotes

Para criar um aplicativo de desgaste 2.0 com o xamarin. Android, você deve adicionar o **wear v2.0** pacote ao seu projeto (clique a **guia Procurar**):

[![V2.0 wear](intro-to-wear-images/wear-nuget-2.0-sml.png "instalar NuGet wear v 2.0")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Este pacote NuGet contém associações para bibliotecas portáteis do Android suporte tanto o desgaste de compatibilidade.

Além **wear**, é recomendável que você instale o **Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "instalar o Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Principais recursos de desgaste 2.0

2.0 Android Wear é a maior atualização para o Android Wear desde seu lançamento inicial em 2014. As seções a seguir destacam os principais recursos do 2.0 Android Wear e são fornecidos links para ajudar você a começar a usar esses novos recursos em seu aplicativo. 


#### <a name="complications"></a>Complicações

*Complicações* é Assista pequena widgets de face que você pode ver rapidamente sem ter que passe o dedo do Mostrador do relógio. Complicações são semelhantes de widgets de painel de estilo de área de trabalho; eles exibem informações como a previsão do tempo, vida útil da bateria, eventos de calendário e estatísticas de aplicativo de aptidão: 

![Exemplo de complicações](intro-to-wear-images/complications.png "exemplo complicações")

Para obter mais informações sobre as complicações, consulte o Android [complicações do Watch Face](https://developer.android.com/wear/preview/features/complications.html) tópico. 



#### <a name="navigation-and-action-drawers"></a>Navegação e gavetas de ação 

Dois gavetas novos são incluídas no Wear 2.0. O *gaveta de navegação*, que aparece na parte superior da tela, permite aos usuários navegar entre os modos de exibição do aplicativo (conforme mostrado à esquerda abaixo). O *gaveta de ação*, que aparece na parte inferior da tela (conforme mostrado no lado direito), permite aos usuários escolher de uma lista de ações. 

![Navegação e ação gavetas](intro-to-wear-images/drawers.png "gavetas de ação e navegação")

Para obter mais informações sobre esses dois gavetas de interativas novo, consulte o Android [Wear navegação e ações](https://developer.android.com/wear/preview/features/ui-nav-actions.html) tópico. 



#### <a name="curved-layouts"></a>Layouts de curvos 

Desgaste 2.0 apresenta novos recursos para a exibição de layouts de curvos em dispositivos de desgaste redondos. Especificamente, o novo `WearableRecyclerView` aula é otimizada para exibir uma lista de itens verticais em round exibe: 

![Exemplo de Layout curvo](intro-to-wear-images/curved-layout.png "exemplo de Layout em curva")

`WearableRecyclerView` estende o `RecyclerView` classe para dar suporte a layouts de curvos e gestos de rolagem circulares. Para obter mais informações, consulte o Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) documentação da API. 



#### <a name="standalone-apps"></a>Aplicativos autônomos 

Aplicativos do Android Wear 2.0 podem trabalhar independentemente dos aplicativos portáteis. Isso significa que, por exemplo, um relógio inteligente pode continuar a oferecer a funcionalidade completa, mesmo se o dispositivo de mão complementar está ativado ou desativado longe do dispositivo portáteis. Para obter mais informações sobre esse recurso, consulte o Android [aplicativos autônomos](https://developer.android.com/wear/preview/features/standalone-apps.html) tópico.



#### <a name="wrist-gestures"></a>Gestos de pulso 

Gestos de pulso possibilitam que os usuários interajam com seu aplicativo sem usar a tela touch &ndash; os usuários possam responder para o aplicativo com uma única mão. Há suporte para dois gestos de pulso: 

-   Pulso de movimento-out
-   Pulso de movimento em

Para obter mais informações, consulte o Android [gestos de pulso](https://developer.android.com/wear/preview/features/gestures.html) tópico. 


Há muitos outros recursos de desgaste 2.0 como ações embutidas, resposta inteligente, entrada remota, notificações expandidas e um novo modo de ponte para notificações. Para obter mais informações sobre os novos recursos de desgaste 2.0, consulte o Android [visão geral da API](https://developer.android.com/wear/preview/api-overview.html). 



## <a name="devices"></a>Dispositivos

Aqui estão alguns exemplos de dispositivos que podem ser executados Android Wear:

* [Motorola 360](https://moto360.motorola.com/)
* [LG G Watch](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G Watch R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung engrenagem ao vivo](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>Leitura adicional

Confira a documentação do Android Wear do Google:

* [Sobre o Android Wear](http://www.android.com/wear/)
* [Design de aplicativo do Android Wear](https://developer.android.com/design/wear/index.html)
* [biblioteca de Android.support.wearable ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android Wear 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>Resumo

Esta introdução forneceu uma visão geral do Android Wear. Ele descritos os recursos básicos do Android Wear e incluída uma visão geral dos recursos introduzidos no 2.0 Android Wear. Ele fornece links para leitura essencial para ajudar os desenvolvedores a introdução ao desenvolvimento do xamarin. Android Wear e ele lista de exemplos de alguns dispositivos Android Wear atualmente no mercado.


## <a name="related-links"></a>Links relacionados

- [Instalação e configuração](~/android/wear/get-started/installation.md)
- [Introdução](~/android/wear/get-started/index.md)
