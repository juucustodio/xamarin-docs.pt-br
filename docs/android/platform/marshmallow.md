---
title: Recursos de Marshmallow
description: Este artigo ajuda você a começar a usar usando o xamarin. Android para desenvolver aplicativos para Android Marshmallow 6.0.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a396f4fe59db36b134843d2538bcb470a452a85b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157979"
---
# <a name="marshmallow-features"></a>Recursos de Marshmallow

_Este artigo ajuda você a começar a usar usando o xamarin. Android para desenvolver aplicativos para Android Marshmallow 6.0._

Este artigo fornece uma descrição dos novos recursos no Android Marshmallow 6.0, explica como preparar o xamarin. Android para desenvolvimento Android Marshmallow e fornece links para aplicativos de exemplo que ilustram como usar de novo Android Marshmallow recursos em aplicativos xamarin. Android. 


## <a name="overview"></a>Visão geral

[Android Marshmallow 6.0](https://developer.android.com/about/versions/marshmallow/index.html), é a próxima grande do Android versão após Android Lollipop.
Xamarin. Android dá suporte ao Android Marshmallow e inclui:

-   **API 23/Android 6.0 associações** &ndash; Android 6.0 adiciona várias APIs novas para os novos recursos descritos abaixo; essas APIs estão disponíveis para aplicativos xamarin. Android quando você direciona o nível 23 da API. Para obter mais informações sobre APIs do Android 6.0, consulte [APIs do Android 6.0](https://developer.android.com/preview/api-overview.html). 

[![Imagens de Hero de tablets e telefones executando Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Embora a versão Marshmallow se concentra principalmente em "polonês e qualidade", ele também fornece muitos recursos novos de interesse para desenvolvedores do xamarin. Android. Esses recursos incluem: 

-   **Permissões de tempo de execução** &ndash; esse aprimoramento permite que os usuários aprovar permissões de segurança caso a caso em tempo de execução. 

-   **Melhorias de autenticação** &ndash; começando com o Android Marshmallow, aplicativos podem agora usar sensores de impressão digital para autenticar usuários e uma nova *confirmar credencial* recurso minimiza a necessidade de inserir senhas. 

-   **Vinculação de aplicativo** &ndash; esse recurso ajuda a eliminar a necessidade de ter o **seletor de aplicativo** pop-up associando automaticamente aplicativos com domínios da web. 

-   **Direcionar o compartilhamento** &ndash; você pode definir *direcionar o compartilhamento de destinos* que torne o compartilhamento rápido e intuitivo para os usuários; esse recurso permite uers compartilhar conteúdo com outros aplicativos. 

-   **Interações de voz** &ndash; essa nova API permite que você crie os recursos de voz conversação em seu aplicativo. 

-   **Modo de exibição de 4 mil** &ndash; no Android Marshmallow, seu aplicativo pode solicitar um 4K resolução de vídeo no hardware que dá suporte a ele. 

-   **Novos recursos de áudio** &ndash; começando com Marshmallow, Android agora é compatível com o protocolo MIDI. Ele também fornece novas classes para criar objetos de reprodução e captura de áudio digital e oferece novo ganchos de API para a associação de dispositivos de áudio e de entrada. 

-   **Novos recursos de vídeo** &ndash; Marshmallow fornece uma nova classe que ajuda os aplicativos a renderizar fluxos de áudio e vídeos em sincronia; essa classe também fornece suporte para a taxa de reprodução dinâmico. 

-   **Android for Work** &ndash; Marshmallow inclui controles aprimorados para dispositivos corporativos, usuário único. Ele dá suporte à instalação silenciosa e desinstalar aplicativos pelo proprietário do dispositivo, a aceitação automática de atualizações do sistema, gerenciamento de certificados aprimorada, acompanhamento de uso de dados, gerenciamento de permissões e notificações de status do trabalho. 

-   **Biblioteca de suporte do Design de material** &ndash; nova *biblioteca de suporte do Design* fornece componentes de design e padrões que torna mais fácil para você criar a aparência de Design de Material em seu aplicativo. 

Além disso, muitas atualizações de biblioteca Android core foram lançadas com o Android M, e essas atualizações fornecem novos recursos para Android M e versões anteriores do Android.

Além disso, muitas atualizações de biblioteca Android core foram lançadas com o Android Marshmallow, e essas atualizações fornecem novos recursos para as versões anteriores do Android e Android Marshmallow. Este artigo explica como começar a criar aplicativos com o Android Marshmallow, e ele fornece que uma visão geral do novo recurso destaca no Android 6.0. 

## <a name="requirements"></a>Requisitos

A seguir é necessário para usar os novos recursos do Android Marshmallow em aplicativos baseados em Xamarin: 

-   **Xamarin. Android** &ndash; xamarin. Android 5.1.7.12 ou posterior deve ser instalado e configurado com o Visual Studio ou Xamarin Studio.

-   **O Visual Studio para Mac** ou **Visual Studio** &ndash; se você estiver usando o Visual Studio para Mac, versão 5.9.7.22 ou posterior é necessário. Se você estiver usando o Visual Studio, versão 3.11.1537 ou posterior das ferramentas do Xamarin para Visual Studio é necessária. 

-   **SDK do Android** &ndash; 6.0 do SDK do Android (API 23) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; xamarin. Android exige [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior, se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1.8 também dá suporte a níveis de API anteriores ao 24, incluindo Marshmallow). A versão de 64 bits do JDK 1.8 é necessária se você estiver usando controles personalizados ou o pré-visualizador de formulários.

Você pode continuar a usar [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se você estiver desenvolvendo especificamente para o nível de API 23 ou anterior. 


## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Marshmallow com xamarin. Android, você deve baixar e instalar os pacotes do SDK e ferramentas mais recentes antes de criar um projeto do Android Marshmallow: 

1.  Instalar as atualizações mais recentes do Xamarin do **estável** channel. 

2.  Instale as ferramentas e pacotes do SDK do Android 6.0 Marshmallow.

3.  Crie um novo projeto do xamarin. Android que tem como alvo o Android 6.0 Marshmallow (API nível 23). 

4.  Configure um dispositivo ou emulador para Android Marshmallow.

Cada uma dessas etapas é explicada nas seções a seguir:


### <a name="install-xamarin-updates"></a>Instalar atualizações do Xamarin

Para atualizar o Xamarin para que ele inclui suporte para o Android Marshmallow 6.0, altere o canal de atualização para **estável** e instale todas as atualizações. Para obter mais informações sobre como instalar atualizações do canal de atualizações, consulte [alterar o canal de atualizações](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 


### <a name="install-the-android-60-sdk"></a>Instalar o SDK do Android 6.0

Para criar um projeto xamarin. Android para o Android Marshmallow, primeiro você deve usar o Gerenciador de SDK do Android para instalar o SDK do Android 6.0:

-   Inicie o Gerenciador de SDK do Android (no Visual Studio para Mac, use **Ferramentas > Gerenciador de SDK**; no Visual Studio, use **Ferramentas > Android > Gerenciador de SDK do Android**) e instale o Android SDK Tools mais recente:

    [![Selecionando ferramentas SDK do Android no Gerenciador de SDK do Android](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   Além disso, instale a versão mais recente **Android 6.0** pacotes SDK:

    [![Selecionando pacotes do SDK do Android 6.0 no Gerenciador de SDK do Android](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Você deve instalar a revisão do Android SDK Tools de 24.3.4 ou posterior.
Para obter mais informações sobre como usar o Gerenciador de SDK do Android para instalar o SDK do Android 6.0, consulte [Gerenciador de SDK](https://developer.android.com/tools/help/sdk-manager.html).



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto xamarin. Android

Crie um novo projeto do xamarin. Android. Se você for novo no desenvolvimento de Android com Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos do Android. 

Quando você cria um projeto do Android, você deve configurar as configurações de versão destino Android MarshMallow 6.0. Para direcionar seu projeto para Marshmallow, você deve configurar seu projeto para **API nível 23 (xamarin. Android v6.0 suporte)**. Para obter mais informações sobre como configurar níveis de nível de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).



### <a name="configure-an-emulator-or-device"></a>Configurar um dispositivo ou emulador

Se você estiver usando um emulador, inicie o Gerenciador de AVD do Android e criar um novo dispositivo usando as seguintes configurações:

-   Dispositivo: Nexus 5, 6 ou 9.
-   destino: Android 6.0 - nível 23 da API
-   ABI: x86

Por exemplo, este dispositivo virtual é configurado para emular um Nexus 5:

[![Configurando um AVD usando dispositivos Nexus 5, o destino do Android 6.0 e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se você estiver usando um dispositivo físico, como um Nexus 5, 6 ou 9, você pode instalar uma imagem de visualização do Android Marshmallow. Para obter mais informações sobre como atualizar seu dispositivo para Android Marshmallow, consulte [imagens do sistema de Hardware](https://developer.android.com/preview/download.html#images).



## <a name="new-features"></a>Novos recursos

Muitas das alterações introduzidas no Android Marshmallow estão concentradas em melhorar a experiência do usuário do Android, aumentando o desempenho e correção de bugs. No entanto, Marshmallow também introduziu alguns grandes alterações aos conceitos básicos da plataforma Android. As seções a seguir destacam essas melhorias e fornecem links para ajudá-lo a começar a usar os novos recursos do Android Marshmallow em seu aplicativo. 



### <a name="runtime-permissions"></a>Permissões de tempo de execução

O sistema de permissões do Android foi significativamente otimizado e simplificado desde Android Lollipop. No Android Marshmallow, usuários conceder permissões em uma base por caso em tempo de execução em vez de em tempo de instalação. Para dar suporte a esse recurso no Android Marshmallow e versões posteriores, você projetar seu aplicativo para solicitar ao usuário as permissões em tempo de execução (no contexto do qual as permissões são necessárias). Essa alteração torna mais fácil para os usuários começar a usar seu aplicativo imediatamente porque ele simplifica o processo de instalação e atualização de seu aplicativo. 

Ver [solicitando permissões de tempo de execução no Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de permissões de tempo de execução em aplicativos xamarin. Android.
Xamarin também oferece um aplicativo de exemplo que ilustra como permissões de tempo de execução funcionam no Android Marshmallow (e posterior): [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions).

Este aplicativo de exemplo demonstra o seguinte:

-   Como verificar e solicitar permissões em tempo de execução.
-   Como declarar permissões para os dispositivos do Android M.

Para usar este aplicativo de exemplo:

1.  Toque o **câmera** ou **contatos** botões para exibir as permissões de uma caixa de diálogo de solicitação.
2.  Conceda permissão para exibir os fragmentos de câmera ou contatos.

Para obter mais informações sobre os novos recursos de permissões de tempo de execução do Android Marshmallow, consulte [trabalhando com permissões do sistema](https://developer.android.com/preview/features/runtime-permissions.html).



### <a name="authentication-enhancements"></a>Aprimoramentos de autenticação

Android Marshmallow inclui duas melhorias de autenticação que ajudam a eliminar a necessidade de senhas:

-   **Autenticação de impressão digital** &ndash; usa uma verificação de impressão digital para autenticar usuários.

-   **Confirme se a credencial** &ndash; autentica usuários com base em quanto tempo o dispositivo foi desbloqueado.

Os links e os aplicativos de exemplo descritos a seguir podem ajudar você a se tornar familiar com esses novos recursos.


#### <a name="fingerprint-authentication"></a>Autenticação de impressão digital

Em dispositivos que dão suporte a hardware de leitura de impressões digitais, você pode usar o novo `FingerPrintManager` classe para autenticar um usuário.
Para obter mais informações sobre o recurso de autenticação de impressão digital no Android Marshmallow, consulte [autenticação por impressão digital](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

O Xamarin fornece um aplicativo de exemplo que ilustra como usar as impressões digitais registradas para autenticar um usuário em seu aplicativo: [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog).

Para usar este aplicativo de exemplo:

1.  Tocar o **compra** botão para abrir uma caixa de diálogo de autenticação por impressão digital.
2.  Examina em sua impressão digital registrada para autenticar.

Observe que esse aplicativo de exemplo requer um dispositivo com um leitor de impressão digital.
Este aplicativo não armazena sua impressão digital (ou sua senha).



#### <a name="voice-interactions"></a>Interações de voz

O novo recurso de interações de voz, introduzido no Android Marshmallow permite que os usuários de seu aplicativo usar sua voz para confirmar a ações e selecionar em uma lista de opções. Para obter mais informações sobre interações de voz, consulte [visão geral da API de interação de voz](https://developers.google.com/voice-actions/interaction/). 

Ver [adicionar uma conversa para seu aplicativo Android com interações de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de interações de voz em aplicativos xamarin. Android.
Um aplicativo de exemplo está disponível para ilustrar como usar a API de interação de voz em um aplicativo xamarin. Android: [Interações de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).



#### <a name="confirm-credential"></a>Confirme se a credencial

Usando o novo *confirme se a credencial* recurso do Android Marshmallow, você pode liberar os usuários tenham que lembrar e digitar senhas específicas de aplicativo por autenticá-los com base em quanto tempo o dispositivo foi desbloqueado.
Para fazer isso, use a nova `SetUserAuthenticationValidityDurationSeconds` método da `KeyGenerator`. Use o `KeyGuardManager`do `CreateConfirmDeviceCredentialIntent` método para autenticar o usuário de dentro de seu aplicativo novamente. Para obter mais informações sobre esse novo recurso no Android Marshmallow, consulte [credencial confirmar](https://developer.android.com/preview/api-overview.html#confirm-credential).

O Xamarin fornece um aplicativo de exemplo que ilustra como usar as credenciais do dispositivo (por exemplo, PIN, padrão ou senha) em seu aplicativo: [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

Para usar este aplicativo de exemplo:

1.  Configurar uma tela de bloqueio seguro em seu dispositivo (**seguro > Segurança > Screenlock**).
2.  Toque o **compra** botão e confirme se as credenciais de tela de bloqueio seguro.



### <a name="chrome-custom-tabs"></a>Guias personalizadas do Chrome

Os desenvolvedores de aplicativos enfrentam uma opção quando um usuário toca uma URL: o aplicativo pode iniciar um navegador ou use um navegador no aplicativo com base em um `WebView`. Ambas as opções apresentam desafios &ndash; iniciar o navegador é uma alternância de contexto pesada que não é personalizável, ao mesmo tempo `WebView`s não compartilhar estado com o navegador. Além disso, use de `WebView`s pode adicionar uma sobrecarga adicional de manutenção. 

*Guias personalizadas do Chrome* torna possível para você com elegância e facilmente exibir sites com o poder do Chrome sem a necessidade dos usuários deixar seu aplicativo. Esse recurso dá ao seu aplicativo mais controle sobre a experiência do usuário da web; ele faz as transições entre o nativo e web perfeita do conteúdo sem a necessidade de recorrer a um `WebView`. Seu aplicativo também pode afetar como Chrome e o que parece, personalizando o seguinte: 

-   Cor da barra de ferramentas

-   Entrar e sair animações

-   Ações personalizadas no menu da barra de ferramentas e de estouro do Chrome

-   Chrome anterior ao início e o conteúdo buscar previamente (para um carregamento mais rápido)

Para tirar proveito desse recurso em seu aplicativo xamarin. Android, baixe e instale o [biblioteca de guias do Android suporte personalizado](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para obter mais informações sobre esse recurso, consulte [guias personalizadas de cromo](https://developer.chrome.com/multidevice/android/customtabs).



### <a name="material-design-support-library"></a>Biblioteca de suporte do Design de material

Android Lollipop introduzido [Design de Material](http://www.google.com/design/spec/material-design/introduction.html) como uma nova linguagem de design para atualizar a experiência do Android (consulte [tema de Material](~/android/user-interface/material-theme.md) para obter informações sobre como usar o design de material em aplicativos xamarin. Android). Com o Android Marshmallow, Google apresentou o *biblioteca de suporte do Android Design* para tornar mais fácil para o aplicativo de desenvolvedores para adotar o material de design e a aparência. Esta biblioteca inclui os seguintes componentes:

-   **CoordinatorLayout** &ndash; nova `CoordinatorLayout` widget é semelhante ao mas mais eficiente do que um `FrameLayout`. Você pode usar `CoordinatorLayout` como um contêiner para modos de exibição filho ou como um layout de nível superior e ele fornece um `layout_anchor` atributo que pode ser usado para exibições de âncora em relação a outros modos de exibição.

-   **Barras de ferramentas de recolhimento** &ndash; nova `CollapsingToolbarLayout` é uma barra de aplicativo de recolhimento é um wrapper para `Toolbar`. (Observe que o *barra de aplicativo* é o que era anteriormente conhecido como o *barra de ação*.)

-   **Botão de ação de flutuante** &ndash; um botão redondo que denota a ação principal na interface do seu aplicativo.

-   **Flutuante rótulos para edição de texto** &ndash; usa um novo `TextInputLayout` widget (que envolve `EditText`) para mostrar um rótulo flutuante quando uma dica é ocultada quando um usuário insere texto.

-   **Exibição de navegação** &ndash; novo `NavigationView` widget ajuda você a usar a gaveta de navegação de uma maneira que é mais fácil para os usuários navegarem.

-   **Snackbar** &ndash; novo `SnackBar` widget é um mecanismo de feedback leve (semelhante a uma notificação do sistema) que exibe uma breve mensagem na parte inferior da tela, que aparece acima de todos os outros elementos na tela.

-   **Guias de material** &ndash; novo `TabLayout` widget fornece um layout horizontal para exibir guias como forma de implementar a navegação de nível superior em seu aplicativo.

Para aproveitar o [biblioteca de suporte de Design](https://developer.android.com/tools/support-library/features.html#design) em seu aplicativo xamarin. Android, baixe e instale o Xamarin [Design de bibliotecas de suporte de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) pacote do NuGet.

Ver [Design de Material bonito com a biblioteca de Design de suporte ao Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para obter mais detalhes (incluindo exemplos de código) sobre como usar a biblioteca de suporte do Design de Material em aplicativos xamarin. Android.
O Xamarin fornece um aplicativo de exemplo que demonstra a nova biblioteca de Design do Android no xamarin. Android &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare).
Este exemplo demonstra os seguintes recursos da biblioteca de Design:


-   Recolher barra de ferramentas
-   Botão de ação flutuante
-   Ancoragem de modo de exibição
-   NavigationView
-   Snackbar

Para obter mais informações sobre a biblioteca de Design, consulte [biblioteca de suporte do Android Design](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) no blog do desenvolvedor Android.


### <a name="additional-library-updates"></a>Atualizações de biblioteca adicionais

Além do Android Marshmallow, o Google anunciou atualizações relacionadas a várias bibliotecas principais do Android. Xamarin fornece suporte de xamarin. Android para essas atualizações por meio de vários pacotes do NuGet de versão de visualização: 

-   [Serviços do Google Play](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; a versão mais recente do Google Play Services inclui o novo *convites de aplicativo* recurso, o que torna possível para os usuários compartilhar seu aplicativo com amigos. Para obter mais informações sobre esse recurso, consulte [alcance expanda seu aplicativo com convites de aplicativo do Google](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

-   [Android Support Libraries](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; NuGets esses oferecem recursos que estão disponíveis apenas para APIs da biblioteca ao fornecer versões compatíveis com versões anteriores do framework Android APIs. 

-   [Biblioteca do Android portáteis](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; este NuGet inclui associações de serviços do Google Play. A versão mais recente da biblioteca portáteis traz novos recursos (incluindo uma navegação mais fácil para aplicativos personalizados) para a plataforma Android Wear. 


## <a name="summary"></a>Resumo

Este artigo introduzido Android Marshmallow e explicou como instalar e configurar as ferramentas mais recentes e os pacotes para o desenvolvimento do xamarin. Android em Marshmallow. Ele também forneceu uma visão geral dos recursos novos mais interessantes que Android Marshmallow para desenvolvimento do xamarin. Android.


## <a name="related-links"></a>Links relacionados

- [Android Marshmallow 6.0](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obtenha o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Visão geral do recurso](https://developer.android.com/preview/api-overview.html)
- [Notas de Versão](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions (amostra)](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential (amostra)](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog (amostra)](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
