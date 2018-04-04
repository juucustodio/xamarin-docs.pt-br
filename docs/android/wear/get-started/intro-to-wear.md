---
title: Introdução ao desgaste Android
description: Com a introdução Android desgaste do Google, você não está restrito a apenas telefones e tablets quando se trata de desenvolvimento de ótimos aplicativos Android. Suporte do xamarin Android desgaste possibilita a execução de código c# em seu pulso! Esta Introdução fornece uma visão geral básica de desgaste Android, descreve os principais recursos e oferece uma visão geral dos recursos disponíveis no Android 2.0 desgaste. Lista alguns dos dispositivos Android desgaste mais populares e fornece links para documentação do Google Android desgaste essencial para ler mais.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 0ab166bb71c23d456cb70d35a2794717110642fd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-android-wear"></a>Introdução ao desgaste Android

_Com a introdução Android desgaste do Google, você não está restrito a apenas telefones e tablets quando se trata de desenvolvimento de ótimos aplicativos Android. Suporte do xamarin Android desgaste possibilita a execução de código c# em seu pulso! Esta Introdução fornece uma visão geral básica de desgaste Android, descreve os principais recursos e oferece uma visão geral dos recursos disponíveis no Android 2.0 desgaste. Lista alguns dos dispositivos Android desgaste mais populares e fornece links para documentação do Google Android desgaste essencial para ler mais._


## <a name="overview"></a>Visão geral

Desgaste Android é executado em uma variedade de dispositivos, incluindo a primeira geração 360 Motorola, inspeção de variáveis do LG G e a engrenagem Samsung ao vivo. Uma segunda geração, incluindo SmartWatch 3 da Sony, também foi lançada com recursos adicionais, incluindo GPS internos e reprodução de música offline. Para o Android 2.0 desgaste, Google se juntou à LG para duas novas inspeções: Esporte de inspecionar o LG e o estilo de inspecionar LG.

![Dispositivos com Android desgaste 2.0](intro-to-wear-images/hero-image.png "exemplo desgaste 2.0 dispositivos com Android")

Suporte de xamarin 5.0 e posterior oferece suporte ao Android desgaste por meio de nosso 4.4W Android (API de 20) e controles de interface do usuário específica de desgaste um pacote do NuGet adiciona adicionais. Xamarin 5.0 e versões posteriores também inclui a funcionalidade para empacotar seus aplicativos de uso. Pacotes do NuGet também estão disponíveis para Android 2.0 desgaste conforme descrito posteriormente neste guia.


## <a name="android-wear-basics"></a>Noções básicas de desgaste Android

Desgaste Android tem um paradigma de interface do usuário que é diferente daquele portátil de aplicativos do Android. A primeira onda de desgaste aplicativos foram projetados para estender um complemento aplicativo portátil de alguma maneira, mas a partir do Android desgaste 2.0, o desgaste aplicativos pode ser autônomo usado. Quando você implanta um aplicativo de desgaste, ele é fornecido com um aplicativo portátil complementar. Porque a maioria desgaste aplicativos dependem de um aplicativo complementar portátil, eles precisam de alguma maneira de se comunicar com aplicativos portáteis. As seções a seguir descrevem esses cenários de uso e descrevem os recursos essenciais de desgaste Android. 



### <a name="usage-scenarios"></a>Cenários de uso

A primeira versão do Android desgaste concentraram principalmente sobre a extensão atuais aplicativos portáteis com notificações aprimorados e sincronizar dados entre o aplicativo portátil e o aplicativo de vestir. Portanto, esses cenários são relativamente simples de implementar.


#### <a name="wearable-notifications"></a>Notificações de vestir

É a maneira mais simples para dar suporte ao uso de Android aproveitar a natureza compartilhada de notificações entre o portátil e o dispositivo de vestir. Usando a API de suporte v4 notificação e o `WearableExtender` classe (disponível no [biblioteca de suporte de Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), você pode aproveitar os recursos nativos da plataforma, como cartões de estilo de caixa de entrada ou entrada de voz. O [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) exemplo fornece código de exemplo que demonstra como enviar uma lista de notificações para um dispositivo Android desgaste. 



#### <a name="companion-applications"></a>Aplicativos complementares

Outra estratégia é criar um aplicativo completo que é executado de modo nativo no dispositivo de vestir e se conecta a um aplicativo portátil complementar. Um bom exemplo dessa abordagem é o [teste](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) aplicativo de exemplo que demonstra como criar um teste que é executado em um dispositivo portátil e faz perguntas de teste no dispositivo de vestir. 



### <a name="user-interface"></a>Interface do Usuário

O padrão de navegação principal para desgaste é uma série de cartões organizados verticalmente. Cada uma dessas placas pode ter ações associadas que são colocadas em camadas na mesma linha. O `GridViewPager` classe fornece essa funcionalidade, ele segue o mesmo conceito do adaptador `ListView`. Normalmente, você associa o `GridViewPager` com um `FragmentGridPagerAdaptor` (ou `GridPagerAdaptor`) que lhe permite representar células de cada linha e coluna como um `Fragment`: 

[![Use a navegação](intro-to-wear-images/2d-picker-sml.png "desgaste navegação")](intro-to-wear-images/2d-picker.png#lightbox)

Use também faz uso dos botões de ação que consistem em um grande colorido círculo com texto pequeno descrição abaixo dele (como ilustrado acima).  O [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) demonstra como usar `GridViewPager` e `GridPagerAdapter` em um aplicativo de desgaste.

Android 2.0 desgaste adiciona uma gaveta de navegação uma gaveta de ação e botões de ação embutida para a interface do usuário desgaste. Para obter mais informações sobre os elementos de interface de usuário do Android 2.0 desgaste, consulte o Android [anatomia](https://www.google.com/design/spec-wear/system-overview/anatomy.html) tópico. 



### <a name="communications"></a>Comunicações

Android desgaste fornece duas APIs para facilitar a comunicação entre aplicativos vestir e complementar portátil de comunicação diferente: 

**API dados** &ndash; essa API é semelhante a um repositório de dados sincronizados entre o dispositivo de vestir e o dispositivo portátil. Android cuida de propagar alterações entre vestir e portátil quando ele é ideal para fazer isso. Quando o vestir está fora do intervalo, filas de sincronização para uma hora posterior. O ponto de entrada principal para essa API é `WearableClass.DataApi`. Para obter mais informações sobre essa API, consulte o Android [itens de sincronização de dados](https://developer.android.com/training/wearables/data-layer/data-items.html) tópico. 

**API de mensagens** &ndash; essa API possibilita que você use um caminho de comunicações de nível inferior: uma pequena carga é enviada unidirecional sem sincronização entre os aplicativos de mão e vestir.
O ponto de entrada principal para essa API é `WearableClass.MessageApi`.
Para obter mais informações sobre essa API, consulte o Android [enviando e recebendo mensagens](https://developer.android.com/training/wearables/data-layer/messages.html) tópico.

Você pode optar por registrar retornos de chamada para receber essas mensagens por meio de cada uma das interfaces de ouvinte de API ou, Alternativamente, implementar um serviço em seu aplicativo que deriva de `WearableListenerService`.
Este serviço será automaticamente instanciado pela desgaste Android.
O [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) exemplo ilustra como implementar um `WearableListenerService`.



### <a name="deployment"></a>Implantação

Cada aplicativo vestir é implantado com seu próprio arquivo APK inserido no aplicativo principal APK. Este pacote é tratado automaticamente no xamarin 5.0 e versões posteriores, mas deve ser executado manualmente para versões do xamarin anteriores à versão 5.0. 
[Trabalhando com pacotes](~/android/wear/deploy-test/packaging.md) explica a implantação em mais detalhes. 



## <a name="going-further"></a>Continuar 

É a melhor maneira de se familiarizar com o uso de Android compilar e testar seu primeiro aplicativo. A lista a seguir fornece uma ordem de leitura recomendada para ajudá-lo a se familiarizar rapidamente:

1.  [Instalação e configuração](~/android/wear/get-started/installation.md) fornece instruções detalhadas para instalar e configurar seu ambiente de desenvolvimento para criar aplicativos de desgaste xamarin. 

2.  Depois de você instalar os pacotes necessários e configurar um dispositivo ou emulador, consulte [desgaste Hello,](~/android/wear/get-started/hello-wear.md) para obter instruções passo a passo que explicam como criar um projeto de desgaste Android pequeno botão identificadores clica e exibe um Clique em contador no dispositivo desgaste. 

3.  [Implantação e teste](~/android/wear/deploy-test/index.md) fornece informações mais detalhadas sobre como configurar e implantar em emuladores e dispositivos, incluindo instruções sobre como implantar seu aplicativo em um dispositivo de desgaste via Bluetooth.

4.  [Trabalhando com tamanhos de telas](~/android/wear/screen-sizes.md) explica como visualizar e otimizar sua interface de usuário para vários tamanhos de tela disponível em dispositivos de desgaste. 

5.  [Trabalhando com pacotes](~/android/wear/deploy-test/packaging.md) descreve as etapas para empacotar manualmente desgaste aplicativos para distribuição no Google Play.

Depois que você criou seu primeiro aplicativo de desgaste, convém tentar criar uma face do relógio personalizado para desgaste Android. 
[Criando uma Face do relógio](~/android/wear/platform/creating-a-watchface.md) fornece instruções passo a passo e código de exemplo para desenvolver um extraído para o serviço de face inspecionar digital, seguido do código mais que aprimora a uma face do relógio de estilo analógico com recursos adicionais. 



## <a name="android-wear-20"></a>Android desgaste 2.0

Android 2.0 desgaste apresenta uma variedade de novos recursos e funcionalidades, como *complicações*, layouts, gavetas de navegação e a ação e notificações expandidas em curva. Além disso, desgaste 2.0 possibilita que você crie aplicativos independentes que funcionam independentemente aplicativos portáteis. O novo *gestos de pulso* capacidade permite motora interações com seu aplicativo. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-las em seu aplicativo usar.



### <a name="install-wear-20-packages"></a>Instalar desgaste 2.0 pacotes

Para criar um aplicativo de desgaste 2.0 com o xamarin, você deve adicionar o **v 2.0 do Xamarin.Android.Wear** pacote ao seu projeto (clique o **guia Procurar**):

[![V 2.0 do Xamarin.Android.Wear](intro-to-wear-images/wear-nuget-2.0-sml.png "instalar o NuGet do Xamarin.Android.Wear v 2.0")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Este pacote do NuGet contém associações para o Android suporte vestir e compatibilidade de desgaste bibliotecas.

Além **Xamarin.Android.Wear**, recomendamos que você instale o **Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "instalar o Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Os principais recursos de desgaste 2.0

Android 2.0 desgaste é a maior atualização desgaste Android desde seu lançamento inicial em 2014. As seções a seguir destacam os recursos principais do Android 2.0 desgaste e são fornecidos links para ajudar você a começar a usar esses novos recursos em seu aplicativo. 


#### <a name="complications"></a>Complicações

*Complicações* é inspecionar pequeno widgets face que você pode ver rapidamente sem ter que passar face do relógio. Complicações são semelhantes aos widgets do painel de estilo de área de trabalho; eles exibem informações como o clima, vida útil da bateria, eventos de calendário e estatísticas de aplicativo de adequação: 

![Exemplo de complicações](intro-to-wear-images/complications.png "exemplo complicações")

Para obter mais informações sobre complicações, consulte o Android [inspecionar Face complicações](https://developer.android.com/wear/preview/features/complications.html) tópico. 



#### <a name="navigation-and-action-drawers"></a>Navegação e gavetas de ação 

Dois gavetas novos são incluídas no desgaste 2.0. O *gaveta navegação*, que aparece na parte superior da tela, permite aos usuários navegar entre as exibições de aplicativo (conforme mostrado abaixo à esquerda). O *gaveta ação*, que aparece na parte inferior da tela (conforme mostrado no lado direito), permite que os usuários escolham de uma lista de ações. 

![Navegação e ação gavetas](intro-to-wear-images/drawers.png "navegação e gavetas de ação")

Para obter mais informações sobre esses dois gavetas de interativas novo, consulte o Android [desgaste navegação e ações](https://developer.android.com/wear/preview/features/ui-nav-actions.html) tópico. 



#### <a name="curved-layouts"></a>Layouts de curvados 

Desgaste 2.0 apresenta novos recursos para a exibição de layouts de curvados em dispositivos de desgaste round. Especificamente, o novo `WearableRecyclerView` classe foi otimizada para exibir uma lista de itens verticais em round exibe: 

![Exemplo de Layout curvado](intro-to-wear-images/curved-layout.png "exemplo de Layout em curva")

`WearableRecyclerView` estende o `RecyclerView` classe para dar suporte a layouts curvados e circulares gestos de rolagem. Para obter mais informações, consulte o Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) documentação da API. 



#### <a name="standalone-apps"></a>Aplicativos autônomos 

Aplicativos do Android desgaste 2.0 podem funcionar independentemente das aplicativos portáteis. Isso significa que, por exemplo, uma inspeção inteligente pode continuar a oferecer funcionalidade completa, mesmo se o dispositivo portátil complementar é desativado ou muito longe do dispositivo de vestir. Para obter mais informações sobre esse recurso, consulte o Android [aplicativos autônomos](https://developer.android.com/wear/preview/features/standalone-apps.html) tópico.



#### <a name="wrist-gestures"></a>Gestos de pulso 

Gestos de pulso possibilitam que os usuários interajam com o seu aplicativo sem usar a tela sensível ao toque &ndash; os usuários possam responder para o aplicativo com um símbolo de mão único. Há suporte para dois gestos de pulso: 

-   Pulso de movimento out
-   Pulso de movimento em

Para obter mais informações, consulte o Android [gestos de pulso](https://developer.android.com/wear/preview/features/gestures.html) tópico. 


Há muitos outros recursos de desgaste 2.0 como ações embutido, resposta inteligente, entrada remota, notificações expandidas e um novo modo de ponte para notificações. Para obter mais informações sobre os novos recursos de desgaste 2.0, consulte o Android [visão geral da API](https://developer.android.com/wear/preview/api-overview.html). 



## <a name="devices"></a>Dispositivos

Aqui estão alguns exemplos de dispositivos que podem ser executados desgaste Android:

* [Motorola 360](https://moto360.motorola.com/)
* [LG G inspeção](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G inspecionar R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung engrenagem ao vivo](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>Leitura adicional

Confira a documentação de desgaste Android do Google:

* [Sobre desgaste Android](http://www.android.com/wear/)
* [Design de aplicativo do Android desgaste](https://developer.android.com/design/wear/index.html)
* [biblioteca de Android.support.wearable ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android desgaste 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>Resumo

Esta introdução fornecida uma visão geral de desgaste Android. Ele descritos os recursos básicos do Android desgaste e incluída uma visão geral dos recursos introduzidos no Android 2.0 desgaste. Ele fornecidos links para ler essencial para ajudar os desenvolvedores a introdução ao desenvolvimento do xamarin desgaste e ele listado exemplos de alguns dos dispositivos Android desgaste atualmente no mercado.


## <a name="related-links"></a>Links relacionados

- [Instalação e configuração](~/android/wear/get-started/installation.md)
- [Introdução](~/android/wear/get-started/index.md)
