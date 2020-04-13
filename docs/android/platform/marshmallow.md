---
title: Características do Marshmallow
description: Este artigo ajuda você a começar a usar o Xamarin.Android para desenvolver aplicativos para O Android 6.0 Marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: fb1ba92be9527d490b3d34bd4c0e454b0a750837
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019986"
---
# <a name="marshmallow-features"></a>Características do Marshmallow

_Este artigo ajuda você a começar a usar o Xamarin.Android para desenvolver aplicativos para O Android 6.0 Marshmallow._

Este artigo fornece um esboço dos novos recursos no Android 6.0 Marshmallow, explica como preparar o Xamarin.Android para o desenvolvimento do Android Marshmallow, e fornece links para aplicativos de amostra que ilustram como fazer uso de novos recursos do Android Marshmallow em aplicativos Xamarin.Android. 

## <a name="overview"></a>Visão geral

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), é a próxima grande versão do Android depois do Android Lollipop.
Xamarin.Android suporta Android Marshmallow e inclui:

- **API 23/Android 6.0 Bindings** &ndash; Android 6.0 adiciona muitas novas APIs para os novos recursos descritos abaixo; essas APIs estão disponíveis para aplicativos Xamarin.Android quando você mira a API Nível 23. Para obter mais informações sobre as APIs do Android 6.0, consulte [o Android 6.0 APIs](https://developer.android.com/preview/api-overview.html). 

[![Imagens de heróis de tablets e telefones rodando Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Embora a versão marshmallow seja focada principalmente em "polimento e qualidade", ele também fornece muitos novos recursos de interesse para os desenvolvedores do Xamarin.Android. Esses recursos incluem: 

- **Permissões de tempo de** &ndash; execução Esse aprimoramento permite que os usuários aprovem permissões de segurança caso a caso no tempo de execução. 

- **Melhorias** &ndash; de autenticação A partir do Android Marshmallow, os aplicativos agora podem usar sensores de impressão digital para autenticar usuários, e um novo recurso *de credencial de confirmação minimiza* a necessidade de inserir senhas. 

- **Vinculação de** &ndash; aplicativos Este recurso ajuda a eliminar a necessidade de que o **App Chooser** apareça associando automaticamente aplicativos com domínios da Web. 

- **Compartilhamento** &ndash; direto Você pode definir *metas de compartilhamento direto* que tornam o compartilhamento rápido e intuitivo para os usuários; este recurso permite que os uers compartilhem conteúdo com outros aplicativos. 

- **Interações por** &ndash; voz Esta nova API permite que você crie recursos de voz conversacional em seu aplicativo. 

- **Modo** &ndash; de exibição 4K No Android Marshmallow, seu aplicativo pode solicitar uma resolução de exibição 4K no hardware que o suporta. 

- **Novos recursos de** &ndash; áudio Começando com Marshmallow, o Android agora suporta o protocolo MIDI. Ele também fornece novas classes para criar objetos de captura e reprodução de áudio digital, e oferece novos ganchos de API para associar dispositivos de áudio e entrada. 

- **Novos recursos de** &ndash; vídeo O Marshmallow oferece uma nova classe que ajuda os aplicativos a renderizar fluxos de áudio e vídeo em sincronia; esta classe também fornece suporte para taxa de reprodução dinâmica. 

- **O Android for Work** &ndash; Marshmallow inclui controles aprimorados para dispositivos corporativos de usuário único. Ele suporta instalação e desinstalação silenciosa de aplicativos pelo proprietário do dispositivo, aceitação automática de atualizações do sistema, gerenciamento aprimorado de certificados, rastreamento de uso de dados, gerenciamento de permissões e notificações de status de trabalho. 

- **Biblioteca** &ndash; de suporte ao design de materiais A nova *Biblioteca de Suporte ao Design* fornece componentes e padrões de design que facilitam a construção do design de materiais em seu aplicativo. 

Além disso, muitas atualizações principais da biblioteca do Android foram lançadas com o Android M, e essas atualizações fornecem novos recursos tanto para android M quanto para versões anteriores do Android.

Além disso, muitas atualizações principais da biblioteca do Android foram lançadas com o Android Marshmallow, e essas atualizações fornecem novos recursos tanto para android marshmallow quanto para versões anteriores do Android. Este artigo explica como começar a construir aplicativos com o Android Marshmallow, e fornece uma visão geral dos novos destaques de recursos no Android 6.0. 

## <a name="requirements"></a>Requisitos

A seguir, é necessário usar os novos recursos do Android Marshmallow em aplicativos baseados em Xamarin: 

- **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 ou posterior deve ser instalado e configurado com o Visual Studio ou o Xamarin Studio.

- **Visual Studio para Mac** ou **Visual Studio** &ndash; Se você estiver usando o Visual Studio para Mac, a versão 5.9.7.22 ou posterior é necessária. Se você estiver usando o Visual Studio, a versão 3.11.1537 ou posterior das ferramentas Xamarin para o Visual Studio é necessária. 

- **Android SDK** &ndash; Android SDK 6.0 (API 23) ou posterior deve ser instalado através do Android SDK Manager.

- **Java Developer Kit** &ndash; Xamarin.Android requer [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para API nível 24 ou superior (JDK 1.8 também suporta níveis de API antes de 24, incluindo Marshmallow). A versão de 64 bits do JDK 1.8 é necessária se você estiver usando controles personalizados ou o Visualizador de Formulários.

Você pode continuar a usar [o JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível 23 da API ou anterior. 

## <a name="getting-started"></a>Introdução

Para começar a usar o Android Marshmallow com Xamarin.Android, você deve baixar e instalar as ferramentas mais recentes e pacotes SDK antes de criar um projeto Android Marshmallow: 

1. Instale as atualizações mais recentes do Xamarin do canal **Estável.** 

2. Instale os pacotes e ferramentas do Android 6.0 Marshmallow SDK.

3. Crie um novo projeto Xamarin.Android que tem como alvo o Android 6.0 Marshmallow (API Nível 23). 

4. Configure um emulador ou dispositivo para Android Marshmallow.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="install-xamarin-updates"></a>Instale atualizações de Xamarin

Para atualizar xamarin para que ele inclua suporte para o Android 6.0 Marshmallow, altere o canal de atualização para **Estável** e instale todas as atualizações. Para obter mais informações sobre como instalar atualizações do canal de atualizações, consulte [Alterar o Canal de Atualizações](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Instale o Android 6.0 SDK

Para criar um projeto Xamarin.Android para Android Marshmallow, você deve primeiro usar o Android SDK Manager para instalar o Android 6.0 SDK:

- Inicie o Android SDK Manager (no Visual Studio para Mac, use **ferramentas > SDK Manager**; no Visual Studio, use ferramentas > Android > Android **SDK Manager)** e instale as ferramentas mais recentes do Android SDK:

    [![Selecionando ferramentas Android SDK no Android SDK Manager](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Além disso, instale os pacotes mais recentes do **Android 6.0** SDK:

    [![Selecionando pacotes Android 6.0 SDK no Android SDK Manager](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Você deve instalar a revisão 24.3.4 ou posterior do Android SDK Tools.
Para obter mais informações sobre como usar o Android SDK Manager para instalar o Android 6.0 SDK, consulte [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Inicie um Projeto Xamarin.Android

Crie um novo projeto Xamarin.Android. Se você é novo no desenvolvimento do Android com xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre a criação de projetos Android. 

Ao criar um projeto Android, você deve configurar as configurações da versão para atingir o Android 6.0 MarshMallow. Para direcionar seu projeto para Marshmallow, você deve configurar seu projeto para **a API nível 23 (Suporte Xamarin.Android v6.0)**. Para obter mais informações sobre a configuração dos níveis de nível de API do Android, consulte [Entendendo os níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configure um emulador ou dispositivo

Se você estiver usando um emulador, inicie o Android AVD Manager e crie um novo dispositivo usando as seguintes configurações:

- Dispositivo: Nexus 5, 6 ou 9.
- Alvo: Android 6.0 - API Nível 23
- ABI: x86

Por exemplo, este dispositivo virtual está configurado para emular um Nexus 5:

[![Configurando um AVD usando o dispositivo Nexus 5, Android 6.0 Target e Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Se você estiver usando um dispositivo físico como um Nexus 5, 6 ou 9, você pode instalar uma imagem de visualização do Android Marshmallow. Para obter mais informações sobre como atualizar seu dispositivo para Android Marshmallow, consulte [Hardware System Images](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Novos recursos

Muitas das mudanças introduzidas no Android Marshmallow estão focadas em melhorar a experiência do usuário do Android, aumentar o desempenho e corrigir bugs. No entanto, Marshmallow também introduziu algumas mudanças extensivas nos fundamentos da plataforma Android. As seções a seguir destacam esses aprimoramentos e fornecem links para ajudá-lo a começar a usar os novos recursos do Android Marshmallow em seu aplicativo. 

### <a name="runtime-permissions"></a>Permissões de tempo de execução

O sistema Android Permissions foi significativamente otimizado e simplificado desde o Android Lollipop. No Android Marshmallow, os usuários concedem permissões caso a caso em tempo de execução, em vez de no momento da instalação. Para suportar esse recurso no Android Marshmallow e, posteriormente, você projeta seu aplicativo para solicitar ao usuário permissões em tempo de execução (no contexto de onde as permissões são necessárias). Essa mudança torna mais fácil para os usuários começarem a usar seu aplicativo imediatamente porque simplifica o processo de instalação e atualização do seu aplicativo. 

Consulte [solicitar permissões de tempo de execução no Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de permissões de execução em aplicativos Xamarin.Android.
Xamarin também fornece um aplicativo de exemplo que ilustra como as permissões de tempo de execução funcionam no Android Marshmallow (e posteriores): [RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

Este aplicativo de exemplo demonstra o seguinte:

- Como verificar e solicitar permissões no tempo de execução.
- Como declarar permissões para dispositivos Android M.

Para usar este aplicativo de exemplo:

1. Toque nos botões **Câmera** ou **Contatos** para exibir uma caixa de diálogo de solicitação de permissões.
2. Conceda permissão para visualizar fragmentos de câmera ou contatos.

Para obter mais informações sobre os novos recursos de permissões de tempo de execução no Android Marshmallow, consulte [Trabalhando com permissões do sistema](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Aprimoramentos de autenticação

O Android Marshmallow inclui dois aprimoramentos de autenticação que ajudam a eliminar a necessidade de senhas:

- **Autenticação** &ndash; de impressões digitais Usa uma varredura de impressão digital para autenticar usuários.

- **Confirmar credencial** &ndash; autentica usuários com base em quanto tempo o dispositivo foi desbloqueado.

Os links e aplicativos de exemplo descritos a seguir podem ajudá-lo a se familiarizar com esses novos recursos.

#### <a name="fingerprint-authentication"></a>Autenticação de impressão digital

Em dispositivos que suportam hardware de digitalização `FingerPrintManager` de impressões digitais, você pode usar a nova classe para autenticar um usuário.
Para obter mais informações sobre o recurso de autenticação de impressões digitais no Android Marshmallow, consulte [Autenticação de impressão digital](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin fornece um aplicativo de exemplo que ilustra como usar impressões digitais registradas para autenticar um usuário em seu aplicativo: [FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Para usar este aplicativo de exemplo:

1. Toque no botão **Comprar** para abrir uma caixa de diálogo de autenticação de impressão digital.
2. Digitalizar sua impressão digital registrada para autenticar.

Observe que este aplicativo de amostra requer um dispositivo com um leitor de impressões digitais.
Este aplicativo não armazena sua impressão digital (ou sua senha).

#### <a name="voice-interactions"></a>Interações de voz

O novo recurso De Interações por Voz introduzido no Android Marshmallow permite que os usuários do seu aplicativo usem sua voz para confirmar ações e selecionar a partir de uma lista de opções. Para obter mais informações sobre interações de voz, consulte [Visão geral da API de interação com voz](https://developers.google.com/voice-actions/interaction/). 

Consulte [Adicionar uma conversa ao seu aplicativo para Android com interações de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para obter mais detalhes (incluindo exemplos de código) sobre a implementação de interações de voz em aplicativos Xamarin.Android.
Um aplicativo de exemplo está disponível que ilustra como usar a API de interação por voz em um aplicativo Xamarin.Android: [Voice Interactions](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Confirmar credencial

Usando o novo recurso de *credencial confirmação* do Android Marshmallow, você pode liberar os usuários de ter que lembrar e inserir senhas específicas do aplicativo autenticando-as com base no tempo que seu dispositivo foi desbloqueado.
Para fazer isso, você `SetUserAuthenticationValidityDurationSeconds` usa `KeyGenerator`o novo método do . Use `KeyGuardManager`o `CreateConfirmDeviceCredentialIntent` método 'para reautenticar o usuário de dentro do seu aplicativo. Para obter mais informações sobre este novo recurso no Android Marshmallow, consulte [Confirmar credencial](https://developer.android.com/preview/api-overview.html#confirm-credential).

A Xamarin fornece um aplicativo de exemplo que ilustra como usar credenciais de dispositivos (como PIN, padrão ou senha) em seu aplicativo: [ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Para usar este aplicativo de exemplo:

1. Configure uma tela de bloqueio segura no dispositivo **(Secure > Security > Screenlock**).
2. Toque no botão **Comprar** e confirme as credenciais de tela de bloqueio segura.

### <a name="chrome-custom-tabs"></a>Guias personalizadas do Chrome

Os desenvolvedores de aplicativos enfrentam uma escolha quando um usuário toca em uma URL: o `WebView`aplicativo pode iniciar um navegador ou usar um navegador no aplicativo com base em um . Ambas as opções apresentam desafios &ndash; ao iniciar o navegador é `WebView`um switch de contexto pesado que não é personalizável, enquanto s não compartilham estado com o navegador. Além disso, `WebView`o uso de s pode adicionar sobrecarga de manutenção extra. 

*Chrome Custom Tabs* permite que você exiba de forma fácil e elegante sites com o poder do Chrome sem que seus usuários deixem seu aplicativo. Esse recurso dá ao seu aplicativo mais controle sobre a experiência do usuário na Web; torna as transições entre conteúdo nativo e web `WebView`mais perfeitas sem ter que recorrer a um . Seu aplicativo também pode afetar a aparência e a sensação do Chrome, personalizando o seguinte: 

- Cor da barra de ferramentas

- Insira e saia animações

- Ações personalizadas na barra de ferramentas do Chrome e no menu de estouro

- Pré-inicial e pré-rebusca de conteúdo do Chrome (para carregamento mais rápido)

Para aproveitar esse recurso no seu aplicativo Xamarin.Android, baixe e instale a [Biblioteca de Guias Personalizadas de Suporte](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)para Android .
Para obter mais informações sobre esse recurso, consulte [Guias personalizadas do Chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Biblioteca de suporte ao projeto de materiais

O Android Lollipop introduziu [o Material Design](https://www.google.com/design/spec/material-design/introduction.html) como uma nova linguagem de design para atualizar a experiência do Android (veja Material [Theme](~/android/user-interface/material-theme.md) para obter informações sobre o uso de design de material em aplicativos Xamarin.Android). Com o Android Marshmallow, o Google introduziu a *Biblioteca de Suporte* ao Design Android para facilitar que os desenvolvedores de aplicativos adotem a aparência e a sensação de design de material. Esta biblioteca inclui os seguintes componentes:

- **CoordenadorLayout** &ndash; O `CoordinatorLayout` novo widget é semelhante, `FrameLayout`mas mais poderoso do que um . Você pode `CoordinatorLayout` usar como um recipiente para visualizações de crianças `layout_anchor` ou como um layout de nível superior, e fornece um atributo que pode ser usado para ancorar visualizações em relação a outras visualizações.

- **Barras de ferramentas** &ndash; `CollapsingToolbarLayout` em colapso A novidade é uma `Toolbar`barra de aplicativo em colapso que é um invólucro para . (Observe que a barra do *aplicativo* é o que antes era chamado de *barra de ação*.)

- **Botão** &ndash; de ação flutuante Um botão redondo que denota a ação principal na interface do seu aplicativo.

- **Rótulos flutuantes para edição de texto** &ndash; Usa um novo `TextInputLayout` widget (que envolve `EditText`) para mostrar um rótulo flutuante quando uma dica é oculta quando um usuário insere texto.

- **Visualização** &ndash; de `NavigationView` navegação O novo widget ajuda você a usar a gaveta de navegação de uma forma mais fácil para os usuários navegarem.

- **Snackbar** &ndash; O `SnackBar` novo widget é um mecanismo de feedback leve (semelhante a uma torrada) que exibe uma breve mensagem na parte inferior da tela, aparecendo acima de todos os outros elementos na tela.

- **Guias de** &ndash; `TabLayout` material O novo widget fornece um layout horizontal para exibir guias como forma de implementar navegação de alto nível em seu aplicativo.

Para aproveitar a Biblioteca de [Suporte ao Design](https://developer.android.com/tools/support-library/features.html#design) em seu aplicativo Xamarin.Android, baixe e instale o pacote NuGet do Projeto NuGet da Biblioteca de Suporte [Xamarin Xamarin.](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)

Consulte [Beautiful Material Design com a Biblioteca de Design de Suporte](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para Android para obter mais detalhes (incluindo exemplos de código) sobre o uso da Biblioteca de Suporte ao Design de Materiais em aplicativos Xamarin.Android.
Xamarin fornece um aplicativo de exemplo que demos a &ndash; nova biblioteca Android Design no Xamarin.Android [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare).
Esta amostra demonstra as seguintes características da biblioteca Design:

- Barra de ferramentas em colapso
- Botão de ação flutuante
- Ver ancoragem
- NavigationView
- Lanchonete

Para obter mais informações sobre a biblioteca design, consulte [Android Design Support Library](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) no blog do Desenvolvedor Android.

### <a name="additional-library-updates"></a>Atualizações adicionais da biblioteca

Além do Android Marshmallow, o Google anunciou atualizações relacionadas a várias bibliotecas android principais. A Xamarin oferece suporte ao Xamarin.Android para essas atualizações através de vários pacotes NuGet de pré-lançamento: 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; A versão mais recente do Google Play Services inclui o novo recurso *App Invites,* que permite que os usuários compartilhem seu aplicativo com amigos. Para obter mais informações sobre esse recurso, consulte [Expand 'Your App's Reach with Google App Invites](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- [Bibliotecas](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; de suporte para Android Esses NuGets oferecem recursos que só estão disponíveis para APIs de biblioteca, ao mesmo tempo em que fornecem versões compatíveis para trás das APIs de framework do Android. 

- [Android Wearable Library](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; este NuGet inclui vinculações do Google Play Services. A versão mais recente da biblioteca vestível traz novos recursos (incluindo navegação mais fácil para aplicativos personalizados) para a plataforma Android Wear. 

## <a name="summary"></a>Resumo

Este artigo introduziu o Android Marshmallow e explicou como instalar e configurar as ferramentas e pacotes mais recentes para o desenvolvimento do Xamarin.Android no Marshmallow. Ele também forneceu uma visão geral dos novos recursos mais interessantes do Android Marshmallow para o desenvolvimento do Xamarin.Android.

## <a name="related-links"></a>Links relacionados

- [Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obter o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Visão geral do recurso](https://developer.android.com/preview/api-overview.html)
- [Notas de Versão](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [Permissões de tempo de execução (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [Confirmar Credencial (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [Diálogo de impressões digitais (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
