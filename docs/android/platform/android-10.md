---
title: Recursos do Android 10
description: Como começar a desenvolver aplicativos para Android 10 usando o Xamarin. Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: 293fc8e3f8965e3109a0a549897dd23c34c46917
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198467"
---
# <a name="android-10-with-xamarin"></a>Android 10 com Xamarin

_Como começar a desenvolver aplicativos para Android 10 usando o Xamarin. Android._

O Android 10 agora está disponível no Google. Vários novos recursos e APIs estão sendo disponibilizados nesta versão, e muitos deles são necessários para aproveitar os novos recursos de hardware nos dispositivos Android mais recentes.

![Logotipo do Android 10](~/android/platform/android-10-images/android10_black.png)

Este artigo foi estruturado para ajudá-lo a começar a desenvolver aplicativos Xamarin. Android para Android 10. Ele explica como instalar as atualizações necessárias, configurar o SDK e preparar um emulador ou dispositivo para teste. Ele também fornece uma descrição dos novos recursos no Android 10 e fornece código-fonte de exemplo que ilustra como usar alguns dos principais recursos do Android 10.

O Xamarin. Android 10,0 fornece suporte para Android 10. Para obter mais informações sobre o suporte do Xamarin. Android para Android 10, consulte as [notas de versão do xamarin. Android 10,0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Requisitos

A lista a seguir é necessária para usar os recursos do Android 10 em aplicativos baseados no Xamarin:

- **Visual Studio** – o visual Studio 2019 é recomendado. No Windows Update para o Visual Studio 2019 versão 16,3 ou posterior. No macOS, atualize para o Visual Studio 2019 para Mac versão 8,3 ou posterior.
- **Xamarin. Android** -Xamarin. Android 10,0 ou posterior deve ser instalado com o Visual Studio (o Xamarin. Android é instalado automaticamente como parte do **desenvolvimento móvel com** carga de trabalho do .net no Windows e instalado como parte do **Visual Studio para o instalador do Mac**)
- **Java Developer Kit** -o desenvolvimento do Xamarin. Android 10,0 requer o JDK 8. A distribuição da Microsoft do OpenJDK é instalada automaticamente como parte do Visual Studio.
- A API de SDK do Android de **SDK do Android** 29 deve ser instalada por meio do gerenciador de SDK do Android.

## <a name="get-started"></a>Introdução

Para começar a desenvolver aplicativos Android 10 com o Xamarin. Android, você deve baixar e instalar as ferramentas e os pacotes de SDK mais recentes antes de criar seu primeiro projeto Android 10:

1. O **Visual Studio 2019 é recomendado**. Atualize para o Visual Studio 2019 versão 16,3 ou posterior. Se você estiver usando o Visual Studio para Mac 2019, atualize para o Visual Studio 2019 para Mac versão 8,3 ou posterior.
2. Instale pacotes e ferramentas do **Android 10 (API 29)** por meio do Gerenciador do SDK.
    - Plataforma SDK 10 (API 29) do Android
    - Imagem do sistema do Android 10 (API 29)
    - SDK do Android Build – Tools 29.0.0 +
    - SDK do Android plataforma-ferramentas 29.0.0 +
    - Android Emulator 29.0.0 +
3. Crie um novo projeto Xamarin. Android que tenha como destino o Android 10,0.
4. Configure um emulador ou dispositivo para testar aplicativos Android 10.

Cada uma dessas etapas é explicada abaixo:

### <a name="update-visual-studio"></a>Atualizar o Visual Studio

O Visual Studio 2019 é recomendado para a criação de aplicativos Android 10 usando o Xamarin.

Se você estiver usando o Visual Studio 2019, atualize para o Visual Studio 2019 versão 16,3 ou posterior (para obter instruções, consulte [atualizar o visual studio 2019 para a versão mais recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). No macOS, atualize para o Visual Studio 2019 para Mac 8,3 ou posterior (para obter instruções, consulte [atualizar o visual studio 2019 para Mac para a versão mais recente](https://docs.microsoft.com/en-us/visualstudio/mac/update)).

### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o Xamarin. Android 10,0, primeiro você deve usar o SDK do Android Manager para instalar a plataforma SDK para **Android 10 (API nível 29)** .

1. Inicie o Gerenciador do SDK. No Visual Studio, clique em **ferramentas > Android > o Gerenciador de SDK do Android.** Em Visual Studio para Mac, clique em **ferramentas > Gerenciador do SDK.**
2. No canto inferior direito, clique no ícone de engrenagem e selecione **repositório > Google (sem suporte):**

    ![Seleção de repositório do SDK do Android Manager](~/android/platform/android-10-images/sdkrepository.png)

3. Instale os pacotes de **plataforma do SDK 10 do Android** , listados como **SDK do Android plataforma 29** na guia **plataformas** (para obter mais informações sobre como usar o Gerenciador do SDK, consulte [SDK do Android setup](https://docs.microsoft.com/en-us/xamarin/android/get-started/installation/android-sdk)):

    ![Guia plataforma do SDK do Android Manager](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Criar um projeto Xamarin. Android

Crie um novo projeto Xamarin. Android. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Olá, Android](https://docs.microsoft.com/en-us/xamarin/android/get-started/hello-android/index) para saber mais sobre como criar projetos do Xamarin. Android.

Ao criar um projeto do Android, você deve definir as configurações de versão para o Android 10,0 ou posterior de destino. Por exemplo, para direcionar seu projeto para Android 10, você deve configurar o nível de API do Android de destino do seu projeto para **android 10,0 (API 29)** . Isso inclui a versão da **estrutura de destino** e a versão de SDK do Android de **destino** para a API 29 ou posterior. Para obter mais informações sobre como configurar os níveis de API do Android, consulte [noções básicas sobre níveis de API do Android.](https://docs.microsoft.com/en-us/xamarin/android/app-fundamentals/android-api-levels)

![Estrutura de destino do Xamarin. Android](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configurar um dispositivo ou emulador

Se você estiver usando um dispositivo físico, como um pixel, poderá baixar `System` a atualização  >  `System update`  >  `Check for update` do Android 10 acessando as configurações do seu telefone. Se você preferir atualizar seu dispositivo, consulte as instruções sobre como piscar uma imagem de [fábrica](https://developers.google.com/android/ota) ou uma [imagem OTA](https://developers.google.com/android/ota) para seu dispositivo.

Se você estiver usando um emulador, crie um dispositivo virtual para a API nível 29 e selecione uma imagem baseada em x86. Para obter informações sobre como usar o Android Device Manager para criar e gerenciar dispositivos virtuais, consulte [Gerenciando dispositivos virtuais com o Android Device Manager.](https://docs.microsoft.com/en-us/xamarin/android/get-started/installation/android-emulator/device-manager) Para obter informações sobre como usar o Android Emulator para teste e depuração, consulte [Depurando no Android Emulator.](https://docs.microsoft.com/en-us/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>Novos recursos

O Android 10 apresenta uma variedade de novos recursos. Alguns desses novos recursos destinam-se a aproveitar os novos recursos de hardware oferecidos pelos dispositivos Android mais recentes, enquanto outros foram projetados para aprimorar ainda mais a experiência do usuário do Android:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Aprimore seu aplicativo com os recursos e as APIs do Android 10

Em seguida, quando estiver pronto, aprofunde-se no Android 10 e saiba mais sobre os [novos recursos e APIs](https://developer.android.com/preview/api-overview.html) que você pode usar. Aqui estão alguns dos principais recursos para começar a usar o.

Esses recursos são recomendados para cada aplicativo:

- **Tema escuro:**  garanta uma experiência consistente para os usuários que habilitam o tema escuro em todo o sistema, adicionando um [tema](https://developer.android.com/preview/features/darktheme) escuro ou habilitando a [força escura](https://developer.android.com/preview/features/darktheme#force_dark).

![Tema escuro](~/android/platform/android-10-images/darktheme.png)

- **Dê suporte à [navegação](https://developer.android.com/preview/features/gesturalnav)**  do Gestural em seu aplicativo indo para a borda e verificando se os gestos personalizados são complementares aos gestos de navegação do sistema.

![Navegação de gesto](~/android/platform/android-10-images/gesturenavigation.png)

- **Otimizar para Foldables:**  forneça experiências diretas e de ponta a ponta em dispositivos inovadores de hoje, [otimizando para Foldables](https://developer.android.com/preview/features/foldables).

![Dobrável](~/android/platform/android-10-images/foldable.png)

Esses recursos são recomendados se relevantes para seu aplicativo:

- **Mais notificações interativas:**  se suas notificações incluírem mensagens, habilite [respostas sugeridas e ações em notificações](https://developer.android.com/preview/features#smart-suggestions) para envolver os usuários e deixá-los executarem uma ação instantaneamente.
- **Melhor biometria:**  se você usar a autenticação biométrica, vá para [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), a maneira preferida de dar suporte à autenticação de impressão digital em dispositivos modernos.
- **Gravação aprimorada:**  para dar suporte à legenda ou à gravação de jogos, habilite a [captura de reprodução de áudio](https://developer.android.com/preview/features/playback-capture). É uma ótima maneira de alcançar mais usuários e tornar seu aplicativo mais acessível.
- **Melhores codecs:**  para aplicativos de mídia, experimente [AV1](https://en.wikipedia.org/wiki/AV1) para streaming de vídeo e [HDR10 +](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) para alto vídeo de intervalo dinâmico. Para streaming de fala e música, você pode usar a codificação [Opus](http://opus-codec.org/) e, para os músicos, uma  [API MIDI nativa](https://developer.android.com/preview/features/midi)está disponível.
- **APIs de rede melhores:**  se seu aplicativo gerencia dispositivos IOT por Wi-Fi, experimente as novas [APIs](https://developer.android.com/preview/features#peer2peer) de conexão de rede para funções como configurar, baixar ou imprimir.

Esses são apenas alguns dos muitos recursos e APIs novos no Android 10. Para ver todos eles, visite o [site do Android 10 para desenvolvedores](https://developer.android.com/about/versions/10/highlights).

## <a name="behavior-changes"></a>Alterações de comportamento

Quando a versão de destino do Android é definida como a API nível 29, há várias alterações de plataforma que o grava afetam o comportamento do seu aplicativo, mesmo se você não estiver implementando os novos recursos descritos acima. A lista a seguir é um breve resumo dessas alterações:

- [Para garantir a estabilidade e a compatibilidade do aplicativo, a plataforma Android agora restringe as interfaces não-SDK que seu aplicativo pode usar no Android 10](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions).
- A [memória compartilhada foi alterada](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory).
- [Tempo de execução do Android & a exatidão da AoT](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat).
- [As permissões para tentativas de tela inteira devem `USE_FULL_SCREEN_INTENT`solicitar ](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Suporte para Foldables](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Resumo

Este artigo apresentou o Android 10 e explicou como instalar e configurar as ferramentas e os pacotes mais recentes para o desenvolvimento do Xamarin. Android com o Android 10. Ele forneceu uma visão geral dos principais recursos disponíveis no Android 10. Ele inclui links para documentação de API e tópicos para desenvolvedores do Android para ajudá-lo a começar a criar aplicativos para Android 10. Ele também realçou as alterações de comportamento mais importantes do Android 10 que poderiam afetar os aplicativos existentes.

## <a name="related-links"></a>Links relacionados

- [Android 10](https://developer.android.com/about/versions/10)
