---
title: Introdução ao Android Wear
description: Com a introdução do desgaste do Android do Google, você não está mais restrito a apenas telefones e tablets quando se trata de desenvolver ótimos aplicativos Android. O suporte do Xamarin. Android para Android desgaste possibilita a execução C# de código em seu pulso! Esta introdução fornece uma visão geral básica do desgaste do Android, descreve seus principais recursos e oferece uma visão geral dos recursos disponíveis no Android desgaste 2,0. Ele lista alguns dos dispositivos Android de desgaste mais populares e fornece links para a documentação essencial de desgaste do Google Android para leitura adicional.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 80c24765022a916fa36e97aaf47b36435b3f7a7b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70758493"
---
# <a name="introduction-to-android-wear"></a>Introdução ao Android Wear

_Com a introdução do desgaste do Android do Google, você não está mais restrito a apenas telefones e tablets quando se trata de desenvolver ótimos aplicativos Android. O suporte do Xamarin. Android para Android desgaste possibilita a execução C# de código em seu pulso! Esta introdução fornece uma visão geral básica do desgaste do Android, descreve seus principais recursos e oferece uma visão geral dos recursos disponíveis no Android desgaste 2,0. Ele lista alguns dos dispositivos Android de desgaste mais populares e fornece links para a documentação essencial de desgaste do Google Android para leitura adicional._

## <a name="overview"></a>Visão Geral

O desgaste do Android é executado em uma variedade de dispositivos, incluindo a primeira geração Motorola 360, o G Watch do LG e o Samsung engrenagem Live. Uma segunda geração, incluindo a SmartWatch 3 da Sony, também foi lançada com recursos adicionais, incluindo GPS interno e reprodução de música offline. Para o desgaste do Android 2,0, o Google se desenvolveu com o LG para dois novos Watches: o LG Watch esporte e o estilo de inspeção LG.

![Dispositivos Android de desgaste 2,0](intro-to-wear-images/hero-image.png "Exemplo de dispositivos Android de desgaste 2,0")

O Xamarin. Android 5,0 e posterior dá suporte ao desgaste do Android por meio de nosso suporte para Android 4.4 W (API 20) e um pacote NuGet que adiciona controles de interface do usuário adicionais específicos de desgaste. O Xamarin. Android 5,0 e posterior também inclui a funcionalidade para empacotar seus aplicativos de desgaste. Os pacotes NuGet também estão disponíveis para o Android desgaste 2,0, conforme descrito posteriormente neste guia.

## <a name="android-wear-basics"></a>Noções básicas sobre o desgaste do Android

O desgaste do Android tem um paradigma de interface do usuário que difere do que os aplicativos de Android handheld. A primeira onda de aplicativos de desgaste foi projetada para estender um aplicativo portátil complementar de alguma forma, mas a partir do Android desgaste 2,0, aplicativos de desgaste podem ser usados autônomos. Quando você implanta um aplicativo de desgaste, ele é empacotado com um aplicativo portátil complementar. Como a maioria dos aplicativos de desgaste depende de um aplicativo portátil Companion, eles precisam de alguma maneira de se comunicar com os aplicativos de mão. As seções a seguir descrevem esses cenários de uso e descrevem os recursos essenciais de desgaste do Android. 

### <a name="usage-scenarios"></a>Cenários de uso

A primeira versão do desgaste do Android foi focada principalmente na extensão de aplicativos handheld atuais com notificações aprimoradas e na sincronização de dados entre o aplicativo de mão e o aplicativo portátil. Portanto, esses cenários são relativamente simples de implementar.

#### <a name="wearable-notifications"></a>Notificações do portátil

A maneira mais simples de dar suporte ao uso do Android é aproveitar a natureza compartilhada das notificações entre o portátil e o dispositivo portátil. Usando a API de notificação v4 de suporte e a classe `WearableExtender` (disponível na [biblioteca de suporte do Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), você pode aproveitar os recursos nativos da plataforma, como cartões de estilo de caixa de entrada ou entrada de voz. O exemplo de [RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant) fornece um código de exemplo que demonstra como enviar uma lista de notificações para um dispositivo de desgaste do Android. 

#### <a name="companion-applications"></a>Aplicativos complementares

Outra estratégia é criar um aplicativo completo que seja executado nativamente no dispositivo portátil e os pares com um aplicativo portátil complementar. Um bom exemplo dessa abordagem é o aplicativo de exemplo de [teste](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-quiz) , que demonstra como criar um teste que é executado em um dispositivo portátil e faz perguntas sobre o teste no dispositivo portátil. 

### <a name="user-interface"></a>Interface do Usuário

O padrão de navegação principal para desgaste é uma série de cartões organizados verticalmente. Cada um desses cartões pode ter ações associadas que estão em camadas na mesma linha. A classe `GridViewPager` fornece essa funcionalidade; Ele segue o mesmo conceito de adaptador que `ListView`. Normalmente, você associa o `GridViewPager` a um `FragmentGridPagerAdaptor` (ou `GridPagerAdaptor`) que permite representar cada célula de linha e coluna como um `Fragment`: 

[![Navegação de desgaste](intro-to-wear-images/2d-picker-sml.png "Navegação de desgaste")](intro-to-wear-images/2d-picker.png#lightbox)

O desgaste também usa botões de ação que consistem em um círculo de grande cor com texto de descrição pequeno abaixo dele (conforme ilustrado acima).  O exemplo [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager) demonstra como usar `GridViewPager` e `GridPagerAdapter` em um aplicativo de desgaste.

O Android desgaste 2,0 adiciona uma gaveta de navegação, uma gaveta de ações e botões de ação embutidos à interface do usuário de desgaste. Para obter mais informações sobre elementos de interface do usuário do Android desgaste 2,0, consulte o tópico de [anatomia](https://www.google.com/design/spec-wear/system-overview/anatomy.html) do Android. 

### <a name="communications"></a>Via

O desgaste do Android fornece duas APIs de comunicação diferentes para facilitar a comunicação entre aplicativos de portátil e aplicativos de bolso complementares: 

A **API de dados** &ndash; essa API é semelhante a um armazenamento de dados sincronizado entre o dispositivo portátil e o dispositivo portátil. O Android cuida da propagação de alterações entre portátil e portátil quando é ideal para fazer isso. Quando o portátil está fora do intervalo, ele enfileira a sincronização para um momento posterior. O ponto de entrada principal para essa API é `WearableClass.DataApi`. Para obter mais informações sobre essa API, consulte o tópico [itens de dados de sincronização](https://developer.android.com/training/wearables/data-layer/data-items.html) do Android. 

**API de mensagem** &ndash; essa API possibilita que você use um caminho de comunicação de nível inferior: uma carga pequena é enviada unidirecional sem sincronização entre os aplicativos handheld e portátil.
O ponto de entrada principal para essa API é `WearableClass.MessageApi`.
Para obter mais informações sobre essa API, consulte o tópico [mensagens de envio e recebimento](https://developer.android.com/training/wearables/data-layer/messages.html) do Android.

Você pode optar por registrar retornos de chamada para receber essas mensagens por meio de cada uma das interfaces de ouvinte de API ou, como alternativa, implementar um serviço em seu aplicativo derivado de `WearableListenerService`.
Esse serviço será instanciado automaticamente pelo desgaste do Android.
O exemplo de [FindMyPhone](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-findmyphonesample) ilustra como implementar um `WearableListenerService`.

### <a name="deployment"></a>Implantação

Cada aplicativo portátil é implantado com seu próprio arquivo APK inserido no aplicativo principal APK. Esse empacotamento é manipulado automaticamente no Xamarin. Android 5,0 e posterior, mas deve ser executado manualmente para versões do Xamarin. Android anteriores à versão 5,0. 
[Trabalhar com empacotamento explica a](~/android/wear/deploy-test/packaging.md) implantação em mais detalhes. 

## <a name="going-further"></a>Indo mais 

A melhor maneira de se familiarizar com o desgaste do Android é criar e testar seu primeiro aplicativo. A lista a seguir fornece uma ordem de leitura recomendada para ajudá-lo a se familiarizar rapidamente:

1. Instalação [& a instalação](~/android/wear/get-started/installation.md) fornece instruções detalhadas para instalar e configurar seu ambiente de desenvolvimento para criar aplicativos de desgaste do Xamarin. Android. 

2. Depois de instalar os pacotes necessários e configurar um emulador ou dispositivo, consulte [Olá, desgaste](~/android/wear/get-started/hello-wear.md) para obter instruções passo a passo que explicam como criar um pequeno projeto de desgaste de Android que lida com cliques de botão e exibe um contador de clique no desgaste Vice. 

3. O [teste de & de implantação](~/android/wear/deploy-test/index.md) fornece informações mais detalhadas sobre como configurar e implantar para emuladores e dispositivos, incluindo instruções sobre como implantar seu aplicativo em um dispositivo de desgaste via Bluetooth.

4. [Trabalhar com tamanhos de tela](~/android/wear/screen-sizes.md) explica como Visualizar e otimizar a interface do usuário para os vários tamanhos de tela disponíveis em dispositivos de desgaste. 

5. [Trabalhar com empacotamento](~/android/wear/deploy-test/packaging.md) descreve as etapas para empacotar manualmente aplicativos de desgaste para distribuição em Google Play.

Depois de criar seu primeiro aplicativo de desgaste, talvez você queira tentar criar uma face de inspeção personalizada para o desgaste do Android. 
A [criação de uma face de inspeção](~/android/wear/platform/creating-a-watchface.md) fornece instruções passo a passo e código de exemplo para o desenvolvimento de um serviço de face de inspeção digital retirado, seguido por mais código que o aprimora para uma face de inspeção de estilo analógico com recursos adicionais. 

## <a name="android-wear-20"></a>Desgaste do Android 2,0

O Android desgaste 2,0 apresenta uma variedade de novos recursos e funcionalidades, como *complicações*, layouts curvos, desenhos de navegação e ação e notificações expandidas. Além disso, o desgaste de 2,0 possibilita que você crie aplicativos autônomos que funcionam independentemente de aplicativos de mão. O novo recurso de *gestos de pulso* permite interações de uma mão com seu aplicativo. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-los em seu aplicativo.

### <a name="install-wear-20-packages"></a>Instalar pacotes de desgaste 2,0

Para criar um aplicativo de desgaste 2,0 com o Xamarin. Android, você deve adicionar o pacote **xamarin. Android. desgaste v 2.0** ao seu projeto (clique na **guia procurar**):

[![Xamarin. Android. desgaste v 2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "Instalar o NuGet do Xamarin. Android. desgaste v 2.0")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Este pacote NuGet contém associações para as bibliotecas de compatibilidade de portátil e desgaste de suporte do Android.

Além de **Xamarin. Android. desgaste**, recomendamos que você instale o NuGet **. GooglePlayServices. portátil da xamarin** : 

[![Xamarin. GooglePlayServices. portátil](intro-to-wear-images/gpsw-nuget-sml.png "Instalar o NuGet Xamarin. GooglePlayServices. portátil")](intro-to-wear-images/gpsw-nuget.png#lightbox)

### <a name="key-features-of-wear-20"></a>Principais recursos do desgaste 2,0

O Android desgaste 2,0 é a maior atualização para o desgaste do Android desde sua inicialização inicial em 2014. As seções a seguir destacam os principais recursos do Android desgaste 2,0 e são fornecidos links para ajudá-lo a começar a usar esses novos recursos em seu aplicativo. 

#### <a name="complications"></a>Complicações

As *complicações* são widgets de rosto pequenos que você pode ver em um relance sem ter que passar o rosto da inspeção. As complicações são semelhantes aos widgets do painel estilo da área de trabalho; Eles exibem informações como o clima, a vida útil da bateria, os eventos de calendário e as estatísticas de aplicativo de ADEQÜAÇÃO: 

![Exemplo de complicações](intro-to-wear-images/complications.png "Exemplo de complicações")

Para obter mais informações sobre complicações, consulte o tópico sobre complicações do Android [Watch](https://developer.android.com/wear/preview/features/complications.html) . 

#### <a name="navigation-and-action-drawers"></a>Desenhos de navegação e ação 

Dois novos empates estão incluídos no desgaste 2,0. A *gaveta de navegação*, que aparece na parte superior da tela, permite que os usuários naveguem entre exibições do aplicativo (como mostrado à esquerda abaixo). A *gaveta de ações*, que aparece na parte inferior da tela (conforme mostrado à direita), permite que os usuários escolham uma lista de ações. 

![Desenhos de navegação e ação](intro-to-wear-images/drawers.png "Desenhos de navegação e ação")

Para obter mais informações sobre esses dois novos desenhos interativos, consulte o tópico [ações e navegação de desgaste](https://developer.android.com/wear/preview/features/ui-nav-actions.html) do Android. 

#### <a name="curved-layouts"></a>Layouts curvos 

O desgaste 2,0 apresenta novos recursos para a exibição de layouts curvos em dispositivos de desgaste de ida e volta. Especificamente, a nova classe `WearableRecyclerView` é otimizada para exibir uma lista de itens verticais em monitores redondos: 

![Exemplo de layout curvo](intro-to-wear-images/curved-layout.png "Exemplo de layout curvo")

`WearableRecyclerView` estende a classe `RecyclerView` para dar suporte a layouts curvos e gestos de rolagem circular. Para obter mais informações, consulte a documentação da API do Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) . 

#### <a name="standalone-apps"></a>Aplicativos autônomos 

Os aplicativos do Android desgaste 2,0 podem trabalhar independentemente dos aplicativos de mão. Isso significa que, por exemplo, uma inspeção inteligente pode continuar a oferecer funcionalidade completa, mesmo que o dispositivo portátil esteja desativado ou longe do dispositivo portátil. Para obter mais informações sobre esse recurso, consulte o tópico [aplicativos Android autônomos](https://developer.android.com/wear/preview/features/standalone-apps.html) .

#### <a name="wrist-gestures"></a>Gestos do pulso 

Gestos de pulso possibilitam que os usuários interajam com seu aplicativo sem usar a tela sensível ao toque &ndash; os usuários podem responder ao aplicativo com uma única mão. Há suporte para dois gestos de pulso: 

- Piscar o pulso
- Pulso de piscar em

Para obter mais informações, consulte o tópico [gestos de punho](https://developer.android.com/wear/preview/features/gestures.html) do Android. 

Há muitos outros recursos de desgaste de 2,0, como ações embutidas, resposta inteligente, entrada remota, notificações expandidas e um novo modo de ponte para notificações. Para obter mais informações sobre os novos recursos 2,0 de desgaste, consulte a [visão geral da API](https://developer.android.com/wear/preview/api-overview.html)do Android. 

## <a name="devices"></a>Dispositivos

Aqui estão alguns exemplos dos dispositivos que podem executar o desgaste do Android:

- [Motorola 360](https://moto360.motorola.com/)
- [LG G Watch](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
- [LG G Watch R](http://www.lg.com/us/smartwatch/g-watch-r)
- [Samsung Gear Live](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
- [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
- [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)

## <a name="further-reading"></a>Leitura adicional

Confira a documentação de desgaste do Android do Google:

- [Sobre o desgaste do Android](http://www.android.com/wear/)
- [Design de aplicativo de desgaste do Android](https://developer.android.com/design/wear/index.html)
- [biblioteca do Android. support. portátil](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [Desgaste do Android 2,0](https://developer.android.com/wear/preview/index.html)

## <a name="summary"></a>Resumo

Essa introdução forneceu uma visão geral do desgaste do Android. Ele descreveu os recursos básicos do desgaste do Android e incluiu uma visão geral dos recursos introduzidos no Android desgaste 2,0. Ele forneceu links para a leitura essencial para ajudar os desenvolvedores a se familiarizarem com o desenvolvimento de desgaste do Xamarin. Android e listaram exemplos de alguns dos dispositivos Android de desgaste no mercado.

## <a name="related-links"></a>Links relacionados

- [Instalação e configuração](~/android/wear/get-started/installation.md)
- [Introdução](~/android/wear/get-started/index.md)
