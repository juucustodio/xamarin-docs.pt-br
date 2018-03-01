---
title: Aplicativo de Player ao vivo do Xamarin
description: Editar e testar aplicativos em tempo real em seu dispositivo iOS ou Android
ms.topic: article
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/10/2017
ms.openlocfilehash: 6ae0439566387e22d939ede83e6b4cb5c1a5a8fe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-live-player-app"></a>Aplicativo de Player ao vivo do Xamarin

![Recurso de visualização](~/media/shared/preview.png)

## <a name="get-the-app"></a>Obtenha o aplicativo

Xamarin Live Player está disponível no iOS App Store e Google Play:

[ ![Fazer o download do iOS App Store](player-images/app-store-badge.svg)](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?ls=1&mt=8) [ ![disponível no Google Play](player-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)



## <a name="using-the-app"></a>Usando o aplicativo

Depois de instalar o aplicativo em seu telefone, siga o [instruções de instalação](~/tools/live-player/install.md) para se conectar ao seu computador. Tente uma da [aplicativos de exemplo](~/tools/live-player/samples.md) para fazê-lo funcionar.

Na inicialização, o aplicativo Xamarin ao vivo Player tem esta aparência (no iOS e Android respectivamente):

![Captura de tela de aplicativo do Player iOS de Live](player-images/app-iphone-sml.png) ![Captura de tela de aplicativo Android do Player ao vivo](player-images/app-android-sml.png)

Quando você pressiona **par para o Visual Studio**, use a câmera para verificar o código de barras que mostra em seu computador:

![Captura de tela do scanner de código de barras do iOS](player-images/scan-iphone-sml.png) ![Captura de tela do scanner de código de barras Android](player-images/scan-android-sml.png)

Se a conexão for bem-sucedida, o código deve ser executado no dispositivo quase imediatamente (como o exemplo de Calculadora):

![Aplicativo de exemplo do cálculo em execução no dispositivo](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>Opções

Pressione o botão informações **(i)** na parte inferior do aplicativo para revelar o **opções** menu:

![Captura de tela do menu Opções](player-images/options.png)

### <a name="logs"></a>Logs

Exibir logs para diagnosticar problemas.

### <a name="settings"></a>Configurações

* Alternar a exibição de erros de compilação e tempo de execução.
* Informações de versão.
* Envie comentários.

![Captura de tela das configurações](player-images/settings.png)

## <a name="managing-devices"></a>Gerenciamento de dispositivos

Para conectar um dispositivo pela primeira vez, siga as instruções em [requisitos & instalação](~/tools/live-player/install.md). Você pode combinar vários dispositivos (por exemplo, um iOS e um Android) e gerenciá-los por meio do IDE.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio, escolha **Ferramentas > Xamarin Live Player > Gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu-vs.png)

Esta janela permite que você faça o seguinte:

- Par de um novo dispositivo por meio do exame do código
- Como alternativa emparelhar um dispositivo, digitando o código exibido em sua tela
- Remover dispositivos existentes na lista

Você também pode acessar essa janela da lista de dispositivos.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

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
