---
title: Recursos do Android 10
description: Como começar a desenvolver aplicativos para Android 10 usando Xamarin.Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: b26d99c131e9759da3077a767476de38712bcb18
ms.sourcegitcommit: a9280318bf7bb69e4e5744ee739e76a9cba36b28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82047567"
---
# <a name="android-10-with-xamarin"></a>Android 10 com Xamarin

_Como começar a desenvolver aplicativos para Android 10 usando Xamarin.Android._

O Android 10 já está disponível no Google. Uma série de novos recursos e APIs estão sendo disponibilizados nesta versão, e muitos deles são necessários para aproveitar os novos recursos de hardware nos dispositivos Android mais recentes.

![Logotipo do Android 10](~/android/platform/android-10-images/android10_black.png)

Este artigo está estruturado para ajudá-lo a começar a desenvolver aplicativos Xamarin.Android para Android 10. Ele explica como instalar as atualizações necessárias, configurar o SDK e preparar um emulador ou dispositivo para testes. Ele também fornece um esboço dos novos recursos no Android 10 e fornece código fonte de exemplo que ilustra como usar alguns dos principais recursos do Android 10.

Xamarin.Android 10.0 oferece suporte para o Android 10. Para obter mais informações sobre o suporte ao Xamarin.Android para Android 10, consulte as notas de versão do [Xamarin.Android 10.0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Requisitos

A lista a seguir é necessária para usar os recursos do Android 10 em aplicativos baseados em Xamarin:

- **Visual Studio** - Visual Studio 2019 é recomendado. Na atualização do Windows para visual studio 2019 versão 16.3 ou posterior. No macOS, atualize para o Visual Studio 2019 para a versão 8.3 do Mac ou posterior.
- **Xamarin.Android** - Xamarin.Android 10.0 ou posterior deve ser instalado com o Visual Studio (Xamarin.Android é automaticamente instalado como parte do desenvolvimento móvel com carga de trabalho **.NET** no Windows e instalado como parte do **Visual Studio para Mac Installer**)
- **Java Developer Kit** - Xamarin.Android 10.0 desenvolvimento requer JDK 8. A distribuição do OpenJDK pela Microsoft é automaticamente instalada como parte do Visual Studio.
- **Android SDK** - Android SDK API 29 deve ser instalado através do Android SDK Manager.

## <a name="get-started"></a>Introdução

Para começar a desenvolver aplicativos para Android 10 com Xamarin.Android, você deve baixar e instalar as ferramentas mais recentes e pacotes SDK antes de criar seu primeiro projeto do Android 10:

1. **Visual Studio 2019 é recomendado**. Atualização para visual studio 2019 versão 16.3 ou posterior. Se você estiver usando o Visual Studio para Mac 2019, atualize para o Visual Studio 2019 para a versão 8.3 do Mac ou posterior.
2. Instale pacotes e ferramentas do **Android 10 (API 29)** através do SDK Manager.
    - Plataforma SDK Android 10 (API 29)
    - Imagem do sistema Android 10 (API 29)
    - Android SDK Build-Tools 29.0.0+
    - Android SDK Plataforma-Ferramentas 29.0.0+
    - Android Emulator 29.0.0+
3. Crie um novo projeto Xamarin.Android que tem como alvo o Android 10.0.
4. Configure um emulador ou dispositivo para testar aplicativos do Android 10.

Cada uma dessas etapas é explicada abaixo:

### <a name="update-visual-studio"></a>Atualizar o Visual Studio

Visual Studio 2019 é recomendado para a construção de aplicativos Android 10 usando Xamarin.

Se você estiver usando o Visual Studio 2019, atualize para o Visual Studio 2019 versão 16.3 ou posterior (para instruções, consulte [Update Visual Studio 2019 para a versão mais recente).](https://docs.microsoft.com/visualstudio/install/update-visual-studio) No macOS, atualize para o Visual Studio 2019 para Mac 8.3 ou posterior (para instruções, consulte [Update Visual Studio 2019 para Mac até a versão mais recente).](https://docs.microsoft.com/visualstudio/mac/update)

### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com xamarin.android 10.0, você deve primeiro usar o Android SDK Manager para instalar a plataforma SDK para **Android 10 (API nível 29)**.

1. Ligue o Gerente sdk. No Visual Studio, clique em **Ferramentas > Android > Android SDK Manager.** No Visual Studio para Mac, clique em **Ferramentas > SDK Manager.**
2. No canto inferior direito, clique no ícone de engrenagem e selecione **Repositório > Google (Sem suporte):**

    ![Seleção de Repositório do Gerente de Android SDK](~/android/platform/android-10-images/sdkrepository.png)

3. Instale os pacotes da **Plataforma SDK do Android 10,** que estão listados como **Plataforma Android SDK 29** na guia **Plataformas** (para obter mais informações sobre o uso do SDK Manager, consulte a [configuração do Android SDK](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)):

    ![Guia da plataforma do Android SDK Manager](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Criar um projeto Xamarin.Android

Crie um novo projeto Xamarin.Android. Se você é novo no desenvolvimento do Android com xamarin, consulte [Hello, Android](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index) para aprender sobre a criação de projetos Xamarin.Android.

Ao criar um projeto Android, você deve configurar as configurações da versão para atingir o Android 10.0 ou posterior. Por exemplo, para direcionar seu projeto para o Android 10, você deve configurar o nível de API android de destino do seu projeto para **o Android 10.0 (API 29)**. Isso inclui tanto a versão **target framework** quanto a versão target do **Android SDK** para a API 29 ou posterior. Para obter mais informações sobre a configuração dos níveis de API do Android, consulte [Entendendo os níveis de API do Android.](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)

![Xamarin.Android Target Framework](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configure um dispositivo ou emulador

Se você estiver usando um dispositivo físico como um Pixel, você pode baixar a atualização do Android 10 indo para o Sistema > atualização do sistema > Verifique para atualização nas configurações do seu telefone. Se preferir mostrar o seu dispositivo, consulte as instruções sobre como piscar uma [imagem de fábrica](https://developers.google.com/android/images) ou imagem [OTA](https://developers.google.com/android/ota) para o seu dispositivo.

Se você estiver usando um emulador, crie um dispositivo virtual para o nível 29 da API e selecione uma imagem baseada em x86. Para obter informações sobre como usar o Gerenciador de Dispositivos Android para criar e gerenciar dispositivos virtuais, consulte [Gerenciando dispositivos virtuais com o Gerenciador de Dispositivos Android.](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager) Para obter informações sobre como usar o Emulador Android para testes e depuração, consulte [Depuração no Emulador do Android.](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>Novos recursos

O Android 10 apresenta uma variedade de novos recursos. Alguns desses novos recursos destinam-se a aproveitar os novos recursos de hardware oferecidos pelos dispositivos Android mais recentes, enquanto outros foram projetados para melhorar ainda mais a experiência do usuário do Android:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Melhore seu aplicativo com recursos e APIs do Android 10

Em seguida, quando estiver pronto, mergulhe no Android 10 e aprenda sobre os [novos recursos e APIs](https://developer.android.com/preview/api-overview.html) que você pode usar. Aqui estão algumas das principais características para começar.

Esses recursos são recomendados para todos os aplicativos:

- **Tema escuro:** garanta uma experiência consistente para usuários que habilitam o tema escuro em todo o sistema adicionando um [Tema](https://developer.android.com/preview/features/darktheme) Escuro ou habilitando [force dark](https://developer.android.com/preview/features/darktheme#force_dark).

![Tema escuro](~/android/platform/android-10-images/darktheme.png)

- **Apoie [a navegação](https://developer.android.com/preview/features/gesturalnav)** gestual em seu aplicativo, indo de ponta a ponta e certificando-se de que seus gestos personalizados sejam complementares aos gestos de navegação do sistema.

![Navegação por gestos](~/android/platform/android-10-images/gesturenavigation.png)

- **Otimizar para dobráveis:** ofereça experiências perfeitas e de ponta a ponta nos dispositivos inovadores [atuais, otimizando para dobráveis.](https://developer.android.com/preview/features/foldables)

![Dobrável](~/android/platform/android-10-images/foldable.png)

Esses recursos são recomendados se forem relevantes para o seu aplicativo:

- **Notificações mais interativas:** Se suas notificações incluem mensagens, habilite [respostas e ações sugeridas em notificações](https://developer.android.com/preview/features#smart-suggestions) para engajar os usuários e deixá-los agir instantaneamente.
- **Melhor biometria:** Se você usar auth biométrico, mude para [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), a maneira preferida de suportar auth de impressão digital em dispositivos modernos.
- **Gravação enriquecida:** Para suportar legendas ou gravação de jogabilidade, habilite a [captura de reprodução de áudio](https://developer.android.com/preview/features/playback-capture). É uma ótima maneira de alcançar mais usuários e tornar seu aplicativo mais acessível.
- **Melhores codecs:** Para aplicativos de mídia, tente [AV1](https://en.wikipedia.org/wiki/AV1) para streaming de vídeo e [HDR10+](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) para vídeo de alto alcance dinâmico. Para o streaming de fala e música, você pode usar a codificação [Opus,](http://opus-codec.org/) e para músicos, uma [API](https://developer.android.com/preview/features/midi) MIDI nativa está disponível.
- **Melhores APIs de rede:** Se o seu aplicativo gerenciar dispositivos IoT via Wi-Fi, experimente as novas [APIs](https://developer.android.com/preview/features#peer2peer) de conexão de rede para funções como configuração, download ou impressão.

Estes são apenas alguns dos muitos novos recursos e APIs no Android 10. Para ver todos eles, visite o site do [Android 10 para desenvolvedores](https://developer.android.com/about/versions/10/highlights).

## <a name="behavior-changes"></a>Alterações de comportamento

Quando a versão target do Android é definida como Nível 29 da API, existem várias alterações na plataforma que afetam o comportamento do seu aplicativo, mesmo que você não esteja implementando os novos recursos descritos acima. A lista a seguir é um breve resumo dessas alterações:

- [Para garantir a estabilidade e compatibilidade do aplicativo, a plataforma Android agora restringe interfaces não-SDK que seu aplicativo pode usar no Android 10](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions).
- [A memória compartilhada mudou.](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory)
- [O tempo de execução do Android & correção ao AOT](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat).
- [As permissões para intenções `USE_FULL_SCREEN_INTENT`de tela cheia devem solicitar ](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Suporte para dobráveis](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Resumo

Este artigo introduziu o Android 10 e explicou como instalar e configurar as ferramentas e pacotes mais recentes para o desenvolvimento do Xamarin.Android com Android 10. Ele forneceu uma visão geral dos principais recursos disponíveis no Android 10. Ele incluiu links para documentação da API e tópicos do Android Developer para ajudá-lo a começar a criar aplicativos para android 10. Ele também destacou as mudanças de comportamento mais importantes do Android 10 que poderiam impactar os aplicativos existentes.

## <a name="related-links"></a>Links relacionados

- [Android 10](https://developer.android.com/about/versions/10)
