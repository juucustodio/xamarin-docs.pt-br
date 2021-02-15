---
title: Recursos do marshmallow
description: Este artigo ajuda você a começar a usar o usando o Xamarin. Android para desenvolver aplicativos para Android 6,0 marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 52c44efa335d81004ce2e3dbf0d9160640118bea
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453539"
---
# <a name="marshmallow-features"></a>Recursos do marshmallow

_Este artigo ajuda você a começar a usar o usando o Xamarin. Android para desenvolver aplicativos para Android 6,0 marshmallow._

Este artigo fornece uma descrição dos novos recursos no Android 6,0 marshmallow, explica como preparar o Xamarin. Android para desenvolvimento de marshmallow do Android e fornece links para aplicativos de exemplo que ilustram como usar novos recursos marshmallow do Android em aplicativos Xamarin. Android. 

## <a name="overview"></a>Visão geral

[Android 6,0 marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), é a próxima versão principal do Android após a pirulito do Android.
O Xamarin. Android dá suporte ao Android marshmallow e inclui:

- Associações da API **23/Android 6,0** &ndash; O Android 6,0 adiciona muitas novas APIs para os novos recursos descritos abaixo; essas APIs estão disponíveis para aplicativos Xamarin. Android quando você direciona o nível de API 23. Para obter mais informações sobre as APIs do Android 6,0, consulte [APIs do android 6,0](https://developer.android.com/preview/api-overview.html). 

[![Imagens Heroes de tablets e telefones que executam o marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Embora a versão do marshmallow se concentre principalmente em "polonês e qualidade", ela também fornece muitos recursos novos de interesse para desenvolvedores do Xamarin. Android. Esses recursos incluem: 

- Permissões de tempo de **execução** &ndash; Esse aprimoramento possibilita que os usuários aprovem as permissões de segurança caso a caso em tempo de execução. 

- **Aprimoramentos** &ndash; de autenticação A partir do Android marshmallow, os aplicativos agora podem usar sensores de impressão digital para autenticar usuários e um novo recurso *confirmar credencial* minimiza a necessidade de inserir senhas. 

- **Vinculação** &ndash; de aplicativos Esse recurso ajuda a eliminar a necessidade de exibir o pop-up do **aplicativo** ao associar automaticamente os aplicativos a domínios da Web. 

- **Compartilhamento direto** &ndash; Você pode definir *destinos de compartilhamento direto* que tornam o compartilhamento rápido e intuitivo para os usuários; Esse recurso permite que o armazenem Compartilhe conteúdo com outros aplicativos. 

- Interações de voz **Voice Interactions** &ndash; Essa nova API permite que você crie recursos de voz de conversação em seu aplicativo. 

- Modo de exibição de **4K** &ndash; No Android marshmallow, seu aplicativo pode solicitar uma resolução de vídeo de 4K em hardware que dá suporte a ele. 

- **Novos recursos** &ndash; de áudio A partir do marshmallow, o Android agora dá suporte ao protocolo MIDI. Ele também fornece novas classes para criar objetos de captura e reprodução de áudio digital, além de oferecer novos ganchos de API para associar dispositivos de entrada e áudio. 

- **Novos recursos** &ndash; de vídeo O marshmallow fornece uma nova classe que ajuda os aplicativos a renderizar fluxos de áudio e vídeo em sincronia; Essa classe também fornece suporte para a taxa de reprodução dinâmica. 

- **Android for Work** &ndash; O marshmallow inclui controles aprimorados para dispositivos corporativos e de usuário único. Ele dá suporte à instalação silenciosa e à desinstalação de aplicativos pelo proprietário do dispositivo, aceitação automática de atualizações do sistema, gerenciamento de certificados aprimorado, acompanhamento de uso de dados, gerenciamento de permissões e notificações de status de trabalho. 

- Biblioteca de suporte de **design de material** &ndash; A nova *biblioteca de suporte de design* fornece componentes e padrões de design que facilitam a criação de aparências de design de material para o seu aplicativo. 

Além disso, muitas atualizações principais da biblioteca do Android foram lançadas com o Android M, e essas atualizações fornecem novos recursos para Android M e versões anteriores do Android.

Além disso, muitas atualizações principais da biblioteca do Android foram lançadas com o Android marshmallow, e essas atualizações fornecem novos recursos para marshmallow do Android e versões anteriores do Android. Este artigo explica como começar a criar aplicativos com o Android marshmallow e fornece uma visão geral dos novos destaques de recursos no Android 6,0. 

## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os novos recursos do Android marshmallow em aplicativos baseados no Xamarin: 

- **Xamarin. Android** &ndash; xamarin. Android 5.1.7.12 ou posterior deve ser instalado e configurado com o Visual Studio ou Xamarin Studio.

- **Visual Studio para Mac** ou **Visual Studio** &ndash; se você estiver usando Visual Studio para Mac, a versão 5.9.7.22 ou posterior é necessária. Se você estiver usando o Visual Studio, a versão 3.11.1537 ou posterior das ferramentas do Xamarin para Visual Studio será necessária. 

- **SDK do Android** &ndash; SDK do Android 6,0 (API 23) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

- **Java Developer Kit** &ndash; O Xamarin. Android requer o [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1,8 também oferece suporte a níveis de API anteriores a 24, incluindo marshmallow). A versão de 64 bits do JDK 1,8 será necessária se você estiver usando controles personalizados ou o Visualizador de formulários.

Você pode continuar a usar o [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior. 

## <a name="getting-started"></a>Introdução

Para começar a usar o Android marshmallow com o Xamarin. Android, você deve baixar e instalar as ferramentas e os pacotes de SDK mais recentes antes de criar um projeto marshmallow do Android: 

1. Instale as últimas atualizações do Xamarin do canal **estável** . 

2. Instale os pacotes e as ferramentas do SDK do Android 6,0 marshmallow.

3. Crie um novo projeto Xamarin. Android que tenha como destino Android 6,0 marshmallow (API nível 23). 

4. Configurar um emulador ou dispositivo para marshmallow Android.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="install-xamarin-updates"></a>Instalar atualizações do Xamarin

Para atualizar o Xamarin para que ele inclua suporte para Android 6,0 marshmallow, altere o canal de atualização para **estável** e instale todas as atualizações. Para obter mais informações sobre como instalar atualizações do canal de atualizações, consulte [alterar o canal de atualizações](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Instalar o SDK do Android 6,0

Para criar um projeto Xamarin. Android para marshmallow Android, você deve primeiro usar o Gerenciador de SDK do Android para instalar o SDK do Android 6,0:

- Inicie o Gerenciador de SDK do Android (em Visual Studio para Mac, use   **ferramentas > Gerenciador de SDK**; no Visual Studio, use **ferramentas > Android > SDK do Android Manager**) e instale a Android SDK Tools mais recente:

    [![Selecionando Ferramentas de SDK do Android no SDK do Android Manager](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Além disso, instale os pacotes mais recentes do SDK do **Android 6,0** :

    [![Selecionando pacotes do SDK do Android 6,0 no Gerenciador de SDK do Android](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Você deve instalar Android SDK Tools revisão 24.3.4 ou posterior.
Para obter mais informações sobre como usar o Gerenciador de SDK do Android para instalar o SDK do Android 6,0, consulte [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto Xamarin. Android

Crie um novo projeto Xamarin. Android. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Olá, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos do Android. 

Ao criar um projeto do Android, você deve definir as configurações de versão para o Android 6,0 MarshMallow de destino. Para direcionar seu projeto para marshmallow, você deve configurar seu projeto para o **nível de API 23 (suporte a Xamarin. Android v 6.0)**. Para saber mais sobre como configurar níveis de nível de API do Android, confira [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md)

### <a name="configure-an-emulator-or-device"></a>Configurar um emulador ou dispositivo

Se você estiver usando um emulador, inicie o Gerenciador de AVD do Android e crie um novo dispositivo usando as seguintes configurações:

- Dispositivo: Nexus 5, 6 ou 9.
- Destino: Android 6,0-API nível 23
- ABI: x86

Por exemplo, este dispositivo virtual está configurado para emular um Nexus 5:

[![Configurando um AVD usando o dispositivo Nexus 5, o destino do Android 6,0 e o Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se você estiver usando um dispositivo físico, como o Nexus 5, 6 ou 9, poderá instalar uma imagem de visualização do Android marshmallow. Para obter mais informações sobre como atualizar seu dispositivo para Android marshmallow, consulte [hardware System images](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Novos recursos

Muitas das alterações introduzidas no Android marshmallow se concentram em melhorar a experiência do usuário do Android, aumentar o desempenho e corrigir os bugs. No entanto, o marshmallow também introduziu algumas alterações abrangentes nos conceitos básicos da plataforma Android. As seções a seguir destacam esses aprimoramentos e fornecem links para ajudá-lo a começar a usar os novos recursos de marshmallow do Android em seu aplicativo. 

### <a name="runtime-permissions"></a>Permissões de tempo de execução

O sistema de permissões do Android foi significativamente otimizado e simplificado desde a pirulito do Android. No Android marshmallow, os usuários concedem permissões caso a caso em tempo de execução em vez de no momento da instalação. Para dar suporte a esse recurso no Android marshmallow e posterior, você cria seu aplicativo para solicitar permissões ao usuário em tempo de execução (no contexto de onde as permissões são necessárias). Essa alteração torna mais fácil para os usuários começarem a usar seu aplicativo imediatamente porque ele simplifica o processo de instalação e atualização de seu aplicativo. 

Consulte [solicitando permissões de tempo de execução no Android marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para obter mais detalhes (incluindo exemplos de código) sobre como implementar permissões de tempo de execução em aplicativos Xamarin. Android.
O Xamarin também fornece um aplicativo de exemplo que ilustra como as permissões de tempo de execução funcionam no Android marshmallow (e posterior): [RuntimePermissions](/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

Este aplicativo de exemplo demonstra o seguinte:

- Como verificar e solicitar permissões em tempo de execução.
- Como declarar permissões para dispositivos Android M.

Para usar este aplicativo de exemplo:

1. Toque nos botões de **câmera** ou de **contatos** para exibir uma caixa de diálogo de solicitação de permissões.
2. Conceda permissão para exibir fragmentos de câmera ou de contatos.

Para obter mais informações sobre os novos recursos de permissões de tempo de execução no Android marshmallow, consulte [trabalhando com permissões do sistema](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Aprimoramentos de autenticação

O Android marshmallow inclui dois aprimoramentos de autenticação que ajudam a eliminar a necessidade de senhas:

- Autenticação de impressão **digital** &ndash; Usa uma verificação de impressão digital para autenticar usuários.

- **Confirmar credencial** &ndash; Autentica usuários com base em quanto tempo o dispositivo foi desbloqueado.

Os links e aplicativos de exemplo descritos a seguir podem ajudá-lo a se familiarizar com esses novos recursos.

#### <a name="fingerprint-authentication"></a>Autenticação de impressão digital

Em dispositivos que dão suporte ao hardware de verificação de impressão digital, você pode usar a nova `FingerPrintManager` classe para autenticar um usuário.
Para obter mais informações sobre o recurso de autenticação de impressão digital no Android marshmallow, consulte [autenticação de impressão digital](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

O Xamarin fornece um aplicativo de exemplo que ilustra como usar as impressões digitais registradas para autenticar um usuário em seu aplicativo: [FingerprintDialog](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Para usar este aplicativo de exemplo:

1. Toque no botão **comprar** para abrir uma caixa de diálogo autenticação de impressão digital.
2. Examine sua impressão digital registrada para autenticar.

Observe que este aplicativo de exemplo requer um dispositivo com um leitor de impressão digital.
Este aplicativo não armazena sua impressão digital (ou sua senha).

#### <a name="voice-interactions"></a>Interações de voz

O novo recurso de interações de voz introduzido no Android marshmallow permite que os usuários do seu aplicativo usem sua voz para confirmar ações e selecionar em uma lista de opções. Para obter mais informações sobre interações de voz, consulte [visão geral da API de interação de voz](https://developers.google.com/voice-actions/interaction/). 

Consulte [Adicionar uma conversa a seu aplicativo Android com interações de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de interações de voz em aplicativos Xamarin. Android.
Um aplicativo de exemplo está disponível para ilustrar como usar a API de interação de voz em um aplicativo Xamarin. Android: [interações de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Confirmar credencial

Usando o novo recurso *confirmar credencial* do Android marshmallow, você pode liberar usuários de ter que se lembrar e inserir senhas específicas do aplicativo Autenticando-as com base em quanto tempo o dispositivo foi desbloqueado.
Para fazer isso, use o novo `SetUserAuthenticationValidityDurationSeconds` método do `KeyGenerator` . Use o `KeyGuardManager` `CreateConfirmDeviceCredentialIntent` método do para autenticar novamente o usuário de dentro de seu aplicativo. Para obter mais informações sobre esse novo recurso no Android marshmallow, consulte [confirmar credencial](https://developer.android.com/preview/api-overview.html#confirm-credential).

O Xamarin fornece um aplicativo de exemplo que ilustra como usar as credenciais do dispositivo (como PIN, padrão ou senha) em seu aplicativo: [ConfirmCredential](/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Para usar este aplicativo de exemplo:

1. Configure uma tela de bloqueio segura em seu dispositivo (**secure > Security > screenlock**).
2. Toque no botão **comprar** e confirme as credenciais da tela de bloqueio seguro.

### <a name="chrome-custom-tabs"></a>Guias personalizadas do Chrome

Os desenvolvedores de aplicativos enfrentam uma opção quando um usuário toca em uma URL: o aplicativo pode iniciar um navegador ou usar um navegador no aplicativo com base em um `WebView` . Ambas as opções apresentam desafios &ndash; que iniciam o navegador é uma opção de contexto pesada que não é personalizável, enquanto `WebView` s não compartilham o estado com o navegador. Além disso, o uso de `WebView` s pode adicionar sobrecarga de manutenção extra. 

As *guias personalizadas do Chrome* possibilitam que você exiba facilmente e elegante os sites com o poder do Chrome sem que os usuários deixem seu aplicativo. Esse recurso dá a seu aplicativo mais controle sobre a experiência na Web do usuário; Ele torna as transições entre o conteúdo nativo e da Web mais simples sem a necessidade de recorrer a um `WebView` . Seu aplicativo também pode afetar a aparência e as funcionalidades do Chrome, personalizando o seguinte: 

- Cor da barra de ferramentas

- Inserir e sair das animações

- Ações personalizadas na barra de ferramentas do Chrome e no menu de estouro

- Pré-início do Chrome e pré-busca de conteúdo (para carregamento mais rápido)

Para aproveitar esse recurso em seu aplicativo Xamarin. Android, baixe e instale a biblioteca de [guias personalizadas de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para obter mais informações sobre esse recurso, consulte [guias personalizadas do Chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Biblioteca de suporte de design de material

A pirulito do Android introduziu o [design de material](https://www.google.com/design/spec/material-design/introduction.html) como uma nova linguagem de design para atualizar a experiência do Android (consulte o [tema do material](~/android/user-interface/material-theme.md) para obter informações sobre como usar o design de material em aplicativos Xamarin. Android). Com o Android marshmallow, o Google introduziu a *biblioteca de suporte de design do Android* para tornar mais fácil para os desenvolvedores de aplicativos adotar a aparência do design de material. Essa biblioteca inclui os seguintes componentes:

- **CoordinatorLayout** &ndash; O novo `CoordinatorLayout` widget é semelhante a, mas mais potente do que um `FrameLayout` . Você pode usar `CoordinatorLayout` como um contêiner para exibições filho ou como um layout de nível superior e fornece um `layout_anchor` atributo que pode ser usado para ancorar exibições em relação a outras exibições.

- **Recolhendo barras de ferramentas** &ndash; O novo `CollapsingToolbarLayout` é uma barra de aplicativo recolhida que é um wrapper para o `Toolbar` . (Observe que a *barra de aplicativo* é o que antes era conhecido como *barra de ação*.)

- Botão de ação **flutuante** &ndash; Um botão redondo que denota a ação principal na interface do seu aplicativo.

- **Rótulos flutuantes para edição de texto** &ndash; Usa um novo   `TextInputLayout` widget (que encapsula `EditText` ) para mostrar um rótulo flutuante quando uma dica é ocultada quando um usuário insere texto.

- **Exibição** &ndash; de navegação O novo `NavigationView` widget ajuda você a usar a gaveta de navegação de uma maneira que seja mais fácil para os usuários navegarem.

- **Snackbar** &ndash; O novo `SnackBar` widget é um mecanismo de comentários leve (semelhante a um sistema de notificação) que exibe uma breve mensagem na parte inferior da tela, aparecendo acima de todos os outros elementos na tela.

- **Guias** &ndash; de material O novo `TabLayout` widget fornece um layout horizontal para exibir guias como uma maneira de implementar a navegação de nível superior em seu aplicativo.

Para aproveitar a biblioteca de [suporte de design](https://developer.android.com/tools/support-library/features.html#design) em seu aplicativo Xamarin. Android, baixe e instale o pacote NuGet de [design da biblioteca de suporte](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) do xamarin xamarin.

Veja o [lindo design de material com a biblioteca de design de suporte do Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para obter mais detalhes (incluindo exemplos de código) sobre como usar a biblioteca de suporte de design de material em aplicativos Xamarin. Android.
O Xamarin fornece um aplicativo de exemplo que demonstra a nova biblioteca de design do Android no Xamarin. Android &ndash; [Cheesesquare](/samples/xamarin/monodroid-samples/android50-cheesesquare).
Este exemplo demonstra os seguintes recursos da biblioteca de design:

- Barra de ferramentas de recolhimento
- Botão de ação flutuante
- Exibir ancoragem
- NavigationView
- Snackbar

Para obter mais informações sobre a biblioteca de design, consulte [biblioteca de suporte de design do Android](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) no blog do desenvolvedor do Android.

### <a name="additional-library-updates"></a>Atualizações de biblioteca adicionais

Além do Android marshmallow, o Google anunciou atualizações relacionadas a várias bibliotecas principais do Android. O xamarin fornece suporte ao Xamarin. Android para essas atualizações por meio de vários pacotes NuGet de versão prévia: 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; A versão mais recente do Google Play Services inclui o novo recurso de *convites de aplicativos* , o que possibilita que os usuários compartilhem seus aplicativos com amigos. Para obter mais informações sobre esse recurso, consulte [expandir o alcance do seu aplicativo com os convites de aplicativos do Google](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- Bibliotecas de suporte do [Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; Essas NuGets oferecem recursos que só estão disponíveis para APIs de biblioteca, enquanto fornecem versões de APIs do Android Framework compatíveis para versões anteriores. 

- Biblioteca portátil do [Android](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; Este NuGet inclui associações de Google Play Services. A versão mais recente da biblioteca portátil traz novos recursos (incluindo navegação mais fácil para aplicativos personalizados) para a plataforma de desgaste do Android. 

## <a name="summary"></a>Resumo

Este artigo introduziu o Android marshmallow e explicou como instalar e configurar as ferramentas e os pacotes mais recentes para o desenvolvimento do Xamarin. Android no marshmallow. Ele também forneceu uma visão geral dos novos recursos mais empolgantes do Android marshmallow para o desenvolvimento do Xamarin. Android.

## <a name="related-links"></a>Links Relacionados

- [Android 6,0 marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obter o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Visão geral do recurso](https://developer.android.com/preview/api-overview.html)
- [Notas de Versão](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (exemplo)](/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (exemplo)](/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (exemplo)](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)