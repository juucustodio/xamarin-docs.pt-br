---
title: Aplicativo de Player ao vivo do Xamarin
description: Editar e testar aplicativos em tempo real em seu dispositivo iOS ou Android
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/10/2017
ms.openlocfilehash: 2ddaba4541af52ae119fbf504fdcc66ba357c113
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarin-live-player-app"></a>Aplicativo de Player ao vivo do Xamarin

![Recurso de visualização](~/media/shared/preview.png)

## <a name="get-the-app"></a>Obtenha o aplicativo

# <a name="androidtabandroid"></a>[Android](#tab/android)

Xamarin Live Player está disponível para o Android da Google Play:

[ ![Disponível no Google Play](images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Para dispositivos Android sem Google Play Player Xamarin ao vivo está disponível por meio de [HockeyApp](https://aka.ms/xlp-hockeyapp) distribuição. Além disso, a visualização inicial compilações para Android pode ser instalado diretamente do Google Play por optar pelo [programa beta aberto](https://play.google.com/apps/testing/com.xamarin.live)

# <a name="iostabios"></a>[iOS](#tab/ios)

Recomendamos que os usuários ingressem o [aplicativo Xamarin ao vivo Player _iOS visualização_ ](https://aka.ms/liveplayeralpha) Aproveite o acesso rápido às melhorias mais recentes por meio de TestFlight.

-----

## <a name="using-the-app"></a>Usando o aplicativo

Depois de instalar o aplicativo em seu telefone, siga o [instruções de instalação](~/tools/live-player/install.md) para se conectar ao seu computador. Tente uma da [aplicativos de exemplo](~/tools/live-player/samples.md) para fazê-lo funcionar.

Na inicialização, o aplicativo Xamarin ao vivo Player tem esta aparência (no iOS e Android respectivamente):

![Captura de tela de aplicativo do Player iOS de Live](player-images/app-iphone-sml.png) ![Captura de tela de aplicativo Android do Player ao vivo](player-images/app-android-sml.png)

Quando você pressiona **par para o Visual Studio**, use a câmera para verificar o código de barras que mostra em seu computador:

![Captura de tela do scanner de código de barras do iOS](player-images/scan-iphone-sml.png) ![Captura de tela do scanner de código de barras Android](player-images/scan-android-sml.png)

Se a conexão for bem-sucedida, o código deve ser executado no dispositivo quase imediatamente (como o [exemplo Calculadora](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Aplicativo de exemplo do cálculo em execução no dispositivo](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>Opções

Pressione o botão informações **(i)** na parte inferior do aplicativo para revelar o **opções** menu:

[ ![Captura de tela do menu Opções](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Logs

Exibir logs para diagnosticar problemas.

### <a name="settings"></a>Configurações

* Alternar a exibição de erros de compilação e tempo de execução.
* Informações de versão.
* Envie comentários.

[ ![Captura de tela das configurações](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Gerenciamento de dispositivos

Para conectar um dispositivo pela primeira vez, siga as instruções em [requisitos & instalação](~/tools/live-player/install.md). Você pode combinar vários dispositivos (por exemplo, um iOS e um Android) e gerenciá-los por meio do IDE.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Visual Studio, escolha **Ferramentas > Xamarin Live Player > Gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu-vs.png)

Esta janela permite que você faça o seguinte:

- Par de um novo dispositivo por meio do exame do código
- Como alternativa emparelhar um dispositivo, digitando o código exibido em sua tela
- Remover dispositivos existentes na lista

Você também pode acessar essa janela da lista de dispositivos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, escolha **Ferramentas > (Xamarin Live Player) gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu.png)

Esta janela permite que você faça o seguinte:

- Par de um novo dispositivo por meio do exame do código
- Como alternativa emparelhar um dispositivo, digitando o código exibido em sua tela
- Remover dispositivos existentes na lista

![Gerenciar dispositivos de janela](player-images/manage.png)

Você também pode acessar essa janela da lista de dispositivos:

![Escolha o Xamarin Live Player dispositivos da lista de dispositivos](player-images/manage-device-menu.png)

-----

Se você tiver qualquer consulte problemas [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).


## <a name="related-links"></a>Links relacionados

- [Limitações](~/tools/live-player/limitations.md)
- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Exemplos do Xamarin Player ao vivo](~/tools/livehttps://developer.xamarin.com/samples.md)
