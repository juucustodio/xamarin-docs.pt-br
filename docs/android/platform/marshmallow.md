---
title: Recursos de Marshmallow
description: "Este artigo ajuda você a começar a usar usando o xamarin para desenvolver aplicativos para o Android Marshmallow 6.0."
ms.topic: article
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: c9ff9eb0b8d013485f0437d470ec49f1c1795d7a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="marshmallow-features"></a>Recursos de Marshmallow

_Este artigo ajuda você a começar a usar usando o xamarin para desenvolver aplicativos para o Android Marshmallow 6.0._

Este artigo fornece uma descrição dos novos recursos no Android Marshmallow 6.0, explica como preparar o xamarin para o desenvolvimento do Android Marshmallow e fornece links para aplicativos de exemplo que ilustram como usar novo Android Marshmallow recursos em aplicativos xamarin. 


## <a name="overview"></a>Visão geral

[Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html), é o próximo Android principal versão após pirulito Android.
Xamarin dá suporte ao Android Marshmallow e inclui:

-   **API 23/Android 6.0 associações** &ndash; Android 6.0 adiciona muitas novas APIs para os novos recursos descritos abaixo; essas APIs estão disponíveis para aplicativos xamarin quando você direcionar API nível 23. Para obter mais informações sobre as APIs do Android 6.0, consulte [APIs do Android 6.0](http://developer.android.com/preview/api-overview.html). 

[![Imagens herói de tablets e telefones executando Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Embora a versão Marshmallow destina-se principalmente em "polonês e qualidade", ele também fornece muitos recursos novos de interesse para os desenvolvedores do xamarin. Esses recursos incluem: 

-   **Permissões de tempo de execução** &ndash; esse aprimoramento permite que os usuários aprovar as permissões de segurança caso a caso em tempo de execução. 

-   **Aprimoramentos de autenticação** &ndash; a partir do Android Marshmallow, aplicativos agora podem usar os sensores de impressões digitais para autenticar usuários e um novo *confirmar credencial* recurso minimiza a necessidade de inserir senhas. 

-   **A vinculação de aplicativo** &ndash; esse recurso ajuda a eliminar a necessidade de ter o **aplicativo seletor** pop-up associando automaticamente aplicativos domínios da web. 

-   **Direcionar compartilhamento** &ndash; você pode definir *direcionar os destinos de compartilhamento* que tornar compartilhamento rápida e intuitiva para os usuários; esse recurso permite uers compartilhem conteúdo com outros aplicativos. 

-   **Interações de voz** &ndash; essa nova API permite que você crie os recursos de voz conversação em seu aplicativo. 

-   **Modo de exibição de 4 K** &ndash; no Android Marshmallow, seu aplicativo pode solicitar um 4K resolução de vídeo no hardware que oferece suporte a ele. 

-   **Novos recursos de áudio** &ndash; partir Marshmallow, Android agora dá suporte ao protocolo MIDI. Ele também fornece novas classes para criar objetos de reprodução e captura de áudio digital e oferece novo ganchos de API para associar os dispositivos de áudio e de entrada. 

-   **Novos recursos de vídeo** &ndash; Marshmallow fornece uma nova classe que ajuda a aplicativos renderizar fluxos de áudio e vídeo em sincronia; essa classe também fornece suporte para a taxa de reprodução dinâmica. 

-   **Android para o trabalho** &ndash; Marshmallow inclui controles avançados para dispositivos corporativos, usuário único. Ele dá suporte à instalação silenciosa e desinstalar aplicativos pelo proprietário do dispositivo, aceitação automática de atualizações do sistema, gerenciamento de certificados aprimorado, acompanhamento de uso de dados, gerenciamento de permissões e notificações de status de trabalho. 

-   **Biblioteca de suporte de Design material** &ndash; novo *biblioteca de suporte do Design* fornece componentes de design e padrões que facilita a criação da aparência Material de Design no seu aplicativo. 

Além disso, muitas atualizações de biblioteca Android core foram lançadas com Android M, e essas atualizações fornecem novos recursos para as versões anteriores do Android e Android M.

Além disso, muitas atualizações de biblioteca Android core foram lançadas com o Android Marshmallow, e essas atualizações fornecem novos recursos para o Android Marshmallow e versões anteriores do Android. Este artigo explica como começar a criar aplicativos com o Android Marshmallow e fornece que uma visão geral do novo recurso destaca no Android 6.0. 

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para usar os novos recursos do Android Marshmallow em aplicativos baseados em Xamarin: 

-   **Xamarin** &ndash; xamarin 5.1.7.12 ou posterior deve ser instalado e configurado com o Visual Studio ou no Xamarin Studio.

-   **O Visual Studio para Mac** ou **Visual Studio** &ndash; se você estiver usando o Visual Studio para Mac, versão 5.9.7.22 ou posterior é necessário. Se você estiver usando o Visual Studio, versão 3.11.1537 ou posterior das ferramentas Xamarin para Visual Studio será necessária. 

-   **SDK do Android** &ndash; Android SDK 6.0 (API 23) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; xamarin requer [1.8 JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1.8 também dá suporte a API níveis anteriores a 24, incluindo Marshmallow). A versão de 64 bits do JDK 1.8 é necessária se você estiver usando controles personalizados ou o Visualizador de formulários.

Você pode continuar a usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se você estiver desenvolvendo especificamente para API nível 23 ou anterior. 


## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Marshmallow com xamarin, você deve baixar e instalar as ferramentas mais recentes e os pacotes SDK antes de criar um projeto Android Marshmallow: 

1.  Instalar as atualizações mais recentes do Xamarin do **estável** canal. 

2.  Instale as ferramentas e os pacotes do SDK do Android Marshmallow de 6.0.

3.  Crie um novo projeto de xamarin que tem como destino Android 6.0 Marshmallow (API nível 23). 

4.  Configure um dispositivo ou emulador para o Android Marshmallow.

Cada uma dessas etapas é explicada nas seções a seguir:


### <a name="install-xamarin-updates"></a>Instalar atualizações do Xamarin

Para atualizar o Xamarin para que ele inclui suporte para o Android Marshmallow 6.0, altere o canal de atualização para **estável** e instalar todas as atualizações. Para obter mais informações sobre como instalar atualizações do canal de atualizações, consulte [alterar o canal atualizações](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/). 


### <a name="install-the-android-60-sdk"></a>Instalar o SDK do Android 6.0

Para criar um projeto do xamarin para o Android Marshmallow, primeiro você deve usar o Gerenciador de SDK do Android para instalar o SDK do Android 6.0:

-   Inicie o Gerenciador de SDK do Android (no Visual Studio para Mac, use **Ferramentas > Gerenciador de SDK**; no Visual Studio, use **Ferramentas > Android > Gerenciador de SDK do Android**) e instale as ferramentas do SDK do Android mais recentes:

    [![Selecionando Ferramentas do SDK do Android no Gerenciador de SDK do Android](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   Além disso, instale a versão mais recente **Android 6.0** pacotes SDK:

    [![Selecionar pacotes de SDK do Android 6.0 no Gerenciador de SDK do Android](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Você deve instalar a revisão de ferramentas do Android SDK 24.3.4 ou posterior.
Para obter mais informações sobre como usar o Gerenciador de SDK do Android para instalar o SDK do Android 6.0, consulte [Manager SDK](http://developer.android.com/tools/help/sdk-manager.html).



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto do xamarin

Crie um novo projeto de xamarin. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos Android. 

Quando você cria um projeto Android, você deve configurar as configurações de versão para o destino Android MarshMallow 6.0. Para direcionar seu projeto para Marshmallow, você deve configurar seu projeto para **API nível 23 (xamarin v 6.0 suporte)**. Para obter mais informações sobre como configurar níveis de nível de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).



### <a name="configure-an-emulator-or-device"></a>Configurar um dispositivo ou emulador

Se você estiver usando um emulador, inicie o Gerenciador de AVD do Android e criar um novo dispositivo com as seguintes configurações:

-   Dispositivo: Nexus 5, 6 ou 9.
-   Destino: Android 6.0 - API nível 23
-   ABI: x86

Por exemplo, este dispositivo virtual está configurado para emular uma Nexus 5:

[![Configurando um AVD usando dispositivo Nexus 5, o destino do Android 6.0 e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se você estiver usando um dispositivo físico, como uma ligação 5, 6 ou 9, você pode instalar uma imagem de visualização do Android Marshmallow. Para obter mais informações sobre como atualizar seu dispositivo para o Android Marshmallow, consulte [imagens do sistema de Hardware](http://developer.android.com/preview/download.html#images).



## <a name="new-features"></a>Novos recursos

Muitas das alterações introduzidas no Android Marshmallow concentram-se em melhorar a experiência do usuário do Android, aumentando o desempenho e corrigir bugs. No entanto, Marshmallow também introduziu alguns grandes alterações para os fundamentos da plataforma Android. As seções a seguir realce esses aprimoramentos e fornecem links para ajudá-lo a começar a usar os novos recursos do Android Marshmallow em seu aplicativo. 



### <a name="runtime-permissions"></a>Permissões de tempo de execução

O sistema de permissões Android foi otimizado e simplificado desde pirulito Android significativamente. No Android Marshmallow, usuários conceder permissões em uma base por caso em tempo de execução em vez de em tempo de instalação. Para dar suporte a esse recurso no Android Marshmallow e versões posteriores, você deve projetar seu aplicativo para solicitar ao usuário para permissões em tempo de execução (no contexto do qual as permissões são necessárias). Essa alteração torna mais fácil para os usuários a começar a usar o aplicativo imediatamente porque ele simplifica o processo de instalação e atualização de seu aplicativo. 

Consulte [solicitando permissões de tempo de execução no Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de permissões de tempo de execução em aplicativos xamarin.
Xamarin também fornece um aplicativo de exemplo que ilustra como as permissões de tempo de execução funcionam no Android Marshmallow (e posterior): [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions).

Este aplicativo de exemplo demonstra o seguinte:

-   Como verificar e solicitar permissões em tempo de execução.
-   Como declarar permissões para dispositivos Android M.

Para usar este aplicativo de exemplo:

1.  Toque na **câmera** ou **contatos** botões para exibir as permissões de uma caixa de diálogo de solicitação.
2.  Conceda permissão para exibir os fragmentos de câmera ou contatos.

Para obter mais informações sobre os novos recursos de permissões de tempo de execução do Android Marshmallow, consulte [trabalhando com permissões do sistema](https://developer.android.com/preview/features/runtime-permissions.html).



### <a name="authentication-enhancements"></a>Aprimoramentos de autenticação

Android Marshmallow inclui dois aprimoramentos de autenticação que ajudam a eliminar a necessidade de senhas:

-   **Autenticação de impressão digital** &ndash; usa uma verificação de impressões digitais para autenticar usuários.

-   **Confirmar a credencial** &ndash; autentica usuários com base em quanto tempo o dispositivo foi desbloqueado.

Os links e os aplicativos de exemplo descritos a seguir podem ajudar você ficar familiarizado com esses novos recursos.


#### <a name="fingerprint-authentication"></a>Autenticação de impressão digital

Em dispositivos que oferecem suporte a impressão digital verificação de hardware, você pode usar o novo `FingerPrintManager` classe para autenticar um usuário.
Para obter mais informações sobre o recurso de autenticação de impressão digital no Android Marshmallow, consulte [autenticação de impressões digitais](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin fornece um aplicativo de exemplo que ilustra como usar as impressões digitais registradas para autenticar um usuário em seu aplicativo: [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog).

Para usar este aplicativo de exemplo:

1.  Tocar o **compra** botão para abrir uma caixa de diálogo de autenticação de impressão digital.
2.  Digitalize sua impressão digital registrado para autenticar.

Observe que este aplicativo de exemplo exige um dispositivo com um leitor de impressão digital.
Este aplicativo não armazena sua impressão digital (ou sua senha).



#### <a name="voice-interactions"></a>Interações de voz

O novo recurso de interações de voz introduzido no Android Marshmallow permite que os usuários do seu aplicativo usar a voz para confirmar ações e selecione uma lista de opções. Para obter mais informações sobre as interações de voz, consulte [visão geral da API de interação de voz](https://developers.google.com/voice-actions/interaction/). 

Consulte [adicionar uma conversa para seu aplicativo Android com interações de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de interações de voz em aplicativos xamarin.
Há um aplicativo de exemplo que ilustra como usar a API de interação de voz em um aplicativo xamarin: [interações de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).



#### <a name="confirm-credential"></a>Confirmar a credencial

Usando o novo *confirmar credencial* recurso do Android Marshmallow, você pode liberar os usuários tenham lembrar e digitar senhas específicas de aplicativo autenticando-las com base em quanto tempo seu dispositivo foi desbloqueado.
Para fazer isso, use o novo `SetUserAuthenticationValidityDurationSeconds` método o `KeyGenerator`. Use o `KeyGuardManager`do `CreateConfirmDeviceCredentialIntent` método para autenticar novamente o usuário de dentro de seu aplicativo. Para obter mais informações sobre esse novo recurso no Android Marshmallow, consulte [confirmar credencial](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin fornece um aplicativo de exemplo que ilustra como usar as credenciais de dispositivo (como senha, padrão ou PIN) em seu aplicativo: [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

Para usar este aplicativo de exemplo:

1.  Configurar uma tela de bloqueio segura em seu dispositivo (**seguro > Segurança > Screenlock**).
2.  Toque na **compra** botão e confirmar as credenciais de tela de bloqueio segura.



### <a name="chrome-custom-tabs"></a>Guias de cromo personalizado

Os desenvolvedores de aplicativos enfrentam uma opção quando um usuário toca uma URL: o aplicativo pode iniciar um navegador ou use um navegador no aplicativo com base em um `WebView`. Ambas as opções apresentam desafios &ndash; iniciar o navegador é uma alternância de contexto pesada que não é personalizável, enquanto `WebView`não compartilhem estado com o navegador. Além disso, usar `WebView`s pode adicionar a sobrecarga de manutenção extra. 

*Chrome personalizado guias* possibilita facilmente e elegância exibir sites com a potência do Chrome sem deixar seu aplicativo aos usuários. Esse recurso dá ao seu aplicativo mais controle sobre a experiência do usuário da web; Isso faz as transições entre nativo e web perfeita de conteúdo sem precisar recorrer a um `WebView`. Seu aplicativo também pode afetar a aparência Chrome e o que está personalizando o seguinte: 

-   Cor da barra de ferramentas

-   Entrar e sair animações

-   Ações personalizadas no menu da barra de ferramentas e de estouro de cromo

-   Chrome anterior ao início e o conteúdo de pré-busca (para carregamento mais rápido)

Para tirar proveito desse recurso em seu aplicativo xamarin, baixe e instale o [biblioteca de guias do Android suporte personalizado](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para obter mais informações sobre esse recurso, consulte [cromo personalizado guias](https://developer.chrome.com/multidevice/android/customtabs).



### <a name="material-design-support-library"></a>Biblioteca de suporte de material de Design

Android pirulito introduzido [Material de Design](http://www.google.com/design/spec/material-design/introduction.html) como uma nova linguagem de design para atualizar a experiência do Android (consulte [Material tema](~/android/user-interface/material-theme.md) para obter informações sobre como usar o design de material em aplicativos xamarin). Com o Android Marshmallow, Google introduziu o *biblioteca de suporte do Android Design* para tornar mais fácil para o aplicativo de desenvolvedores para adotar o material de design aparência. Essa biblioteca inclui os seguintes componentes:

-   **CoordinatorLayout** &ndash; novo `CoordinatorLayout` widget é semelhante mas mais eficiente do que um `FrameLayout`. Você pode usar `CoordinatorLayout` como um contêiner para modos de exibição filho ou como um layout de nível superior e fornece um `layout_anchor` atributo que pode ser usado para exibições de âncora em relação a outros modos de exibição.

-   **Recolhendo barras de ferramentas** &ndash; novo `CollapsingToolbarLayout` é uma barra recolhendo do aplicativo que é um wrapper para `Toolbar`. (Observe que o *barra de aplicativos* é o que era anteriormente conhecido como o *barra de ação*.)

-   **Botão de ação de flutuante** &ndash; um botão redondo que denota a ação primária na interface do seu aplicativo.

-   **Flutuante rótulos para edição de texto** &ndash; usa um novo `TextInputLayout` widget (que encapsula `EditText`) para mostrar um rótulo flutuante quando uma dica ficará oculto quando um usuário insere o texto.

-   **Modo de exibição de navegação** &ndash; novo `NavigationView` widget ajuda você a usar a gaveta de navegação de uma maneira mais fácil para os usuários navegarem.

-   **Snackbar** &ndash; novo `SnackBar` widget é um mecanismo de comentários leve (semelhante a uma notificação do sistema) que exibe uma mensagem na parte inferior da tela, que aparece acima de todos os outros elementos na tela.

-   **Guias de material** &ndash; novo `TabLayout` widget fornece um layout horizontal para exibir as guias como maneira de implementar a navegação de nível superior no seu aplicativo.

Para aproveitar o [biblioteca de suporte do Design](http://developer.android.com/tools/support-library/features.html#design) em seu aplicativo xamarin, baixe e instale o Xamarin [Design de biblioteca de suporte de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) pacote NuGet.

Consulte [lindos Design de Material com a biblioteca de Design de suporte do Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para obter mais detalhes (incluindo exemplos de código) sobre como usar a biblioteca de suporte do Material de Design em aplicativos xamarin.
Xamarin fornece um aplicativo de exemplo que demonstrações a nova biblioteca de Design Android em xamarin &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare).
Este exemplo demonstra os seguintes recursos da biblioteca de Design:


-   Recolher barra de ferramentas
-   Botão de ação flutuante
-   Ancoragem de exibição
-   NavigationView
-   Snackbar

Para obter mais informações sobre a biblioteca de Design, consulte [biblioteca de suporte do Android Design](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) no blog do desenvolvedor Android.


### <a name="additional-library-updates"></a>Atualizações de biblioteca adicionais

Além do Android Marshmallow, Google anunciou atualizações relacionadas a várias bibliotecas de Android core. Xamarin fornece suporte de xamarin para essas atualizações através de vários pacotes de NuGet de versão de visualização: 

-   [Serviços do Google reproduzir](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; a versão mais recente do Google reproduzir Services inclui o novo *aplicativo convida* recurso que possibilita aos usuários compartilhar seu aplicativo com amigos. Para obter mais informações sobre esse recurso, consulte [alcance do expanda seu aplicativo com convites de aplicativo do Google](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

-   [Bibliotecas de suporte a Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; NuGets esses oferecem recursos que só estão disponíveis para a biblioteca de APIs ao fornecer versões compatíveis com versões anteriores com a estrutura do Android APIs. 

-   [Biblioteca de vestir Android](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; este NuGet inclui associações de serviços do Google reproduzir. A versão mais recente da biblioteca de vestir traz novos recursos (incluindo uma navegação mais simples para aplicativos personalizados) para a plataforma Android desgaste. 


## <a name="summary"></a>Resumo

Este artigo introduzido Android Marshmallow e explicou como instalar e configurar as ferramentas mais recentes e os pacotes para o desenvolvimento do xamarin em Marshmallow. Também forneceu uma visão geral dos novos recursos do Android Marshmallow mais interessantes para o desenvolvimento do xamarin.


## <a name="related-links"></a>Links relacionados

- [Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html)
- [Obter o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Visão geral do recurso](https://developer.android.com/preview/api-overview.html)
- [Notas de Versão](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions (exemplo)](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential (exemplo)](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog (exemplo)](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
