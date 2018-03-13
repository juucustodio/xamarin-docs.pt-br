---
title: "Instalação do Xamarin Player ao vivo"
description: Editar e testar aplicativos em tempo real em seu dispositivo iOS ou Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/22/2017
ms.openlocfilehash: 9b801af77eceaf94b0ede98b6af51d55d6029fe6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="xamarin-live-player-setup"></a>Instalação do Xamarin Player ao vivo

Xamarin Live Player permite que você faça edições de ao vivo em seu aplicativo e tem essas alterações refletidas em tempo real no dispositivo. O código é executado dentro do aplicativo Xamarin ao vivo Player – não é necessário para configurar os emuladores ou usar os cabos para implantar! Este artigo descreve como configurar o Xamarin Live Player.

![Recurso de visualização](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1. Obtenha o aplicativo

### <a name="xamarin-live-player-for-android"></a>Player de Xamarin ao vivo para Android
Xamarin Live Player está disponível para o Android da Google Play:

[ ![Disponível no Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Para dispositivos Android sem Google Play Player Xamarin ao vivo está disponível por meio de [HockeyApp](https://aka.ms/xlp-hockeyapp) distribuição. Além disso, a visualização inicial compilações para Android pode ser instalado diretamente do Google Play por optar pelo [programa beta aberto](https://play.google.com/apps/testing/com.xamarin.live)

### <a name="xamarin-live-player-for-ios"></a>Xamarin Live Player para iOS
Recomendamos que os usuários ingressem o [aplicativo Xamarin ao vivo Player _iOS visualização_ ](https://aka.ms/liveplayeralpha) Aproveite o acesso rápido às melhorias mais recentes por meio de TestFlight.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="2-get-visual-studio-2017-preview-on-windows-or-for-mactabsvsmac"></a>2. Obter o Visual Studio 2017 Preview no Windows (ou [para Mac](?tabs=vsmac))

Xamarin Live Player requer:

- Visual Studio de 2017 [15.4](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/#visualstudio2017) ou mais recente.
- Um computador com Visual Studio e um dispositivo na mesma rede Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Usando o Xamarin Live Player pela primeira vez

1. Abra **2017 do Visual Studio**.
2. Vá para **Ferramentas > Opções...**  e selecione o **Xamarin > outros** guia.
3. Escala **habilitar Player ao vivo Xamarin**:

  ![Marque a caixa Habilitar Xamarin Live Player na janela de opções](install-images/vs2017-options.png)

2. Criar ou abrir um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
3. Escolha **Player Live** na lista de dispositivos:

  ![Lista de dispositivos inclui uma opção de Player de Live Xamarin](install-images/devices-empty-windows.png)

  * Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
  * Caso contrário, você será solicitado para emparelhar um dispositivo quando necessário.
4. Pressione a **executar** botão, ou selecione uma das seguintes opções do **executar** ou menu de atalho:

  - **Iniciar sem depuração** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo (o aplicativo é reiniciado como são feitas alterações e salvar o arquivo).
  - **Iniciar depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas não é possível editar o código.

  Como alternativa, selecione **Ferramentas > Xamarin Live Player > Live executar exibição atual**, que permite que você editar o aplicativo e ver as alterações ocorrem no dispositivo. O modo de exibição atual é mostrado (em vez de na tela do aplicativo principal).

5. Se um dispositivo já está emparelhado e o aplicativo Xamarin ao vivo Player está em execução no dispositivo, o código será executado imediatamente!

  Se não há dispositivos de emparelhamento, um código QR aparecerá com instruções sobre como emparelhar um dispositivo:

    ![Par de uma janela de dispositivo](install-images/manage-empty-windows.png)

  Se o dispositivo não pode ser contatado para emparelhamento, pode aparecer um erro.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="2-get-visual-studio-for-mac-or-for-windowstabsvswin"></a>2. Obtenha o Visual Studio para Mac (ou [para Windows](?tabs=vswin))

Xamarin Live Player requer:

- OS X 10.11, macOS 10.12 ou maior
- Visual Studio para Mac
- Um Mac e um dispositivo na mesma rede Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Usando o Player de Xamarin ao vivo pela primeira vez

1. Abra **Visual Studio para Mac**.
2. Vá para **Visual Studio > Preferências...**  e selecione o **projetos > Xamarin Live Player (visualização)** guia.
3. Escala **habilitar Player ao vivo Xamarin**:

  [![Marque a caixa Habilitar Xamarin Live Player na janela de opções](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

2. Criar ou abrir um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
3. Escolha **Player Live** na lista de dispositivos.

  ![Lista de dispositivos inclui uma opção de Player de Live Xamarin](install-images/devices.png)

  * Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
  * Caso contrário, você será solicitado para emparelhar um dispositivo quando necessário.
  * Escolha **Xamarin Player ao vivo dispositivos...**  para gerenciar os dispositivos que você deseja usar com o Xamarin Live Player.

4. Pressione a **executar** botão, ou selecione uma das seguintes opções do **executar** ou menu de atalho:

  ![Opções de menu de execução](install-images/run-menu.png)

  - **Iniciar sem depuração** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo (o aplicativo é reiniciado como são feitas alterações e salvar o arquivo).
  - **Iniciar depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas não é possível editar o código.
  - **Em tempo de execução exibição atual** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo. O modo de exibição atual é mostrado (em vez de na tela do aplicativo principal).

5. Se um dispositivo já está emparelhado e o aplicativo Xamarin ao vivo Player está em execução no dispositivo, o código será executado imediatamente!

  Se nenhum dispositivo está emparelhado, um código QR aparecerá com instruções sobre como emparelhar um dispositivo:

    ![Par de uma janela de dispositivo](install-images/manage-empty.png)

  Se o dispositivo não pode ser contatado para emparelhamento, um erro será exibida:

    ![Não é possível conectar-se a mensagem de erro de dispositivo](install-images/error-cannot-connect.png)


-----

Se você tiver problemas, consulte [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).


## <a name="related-links"></a>Links relacionados

- [Limitações](~/tools/live-player/limitations.md)
- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Exemplos do Xamarin Player ao vivo](~/tools/livehttps://developer.xamarin.com/samples.md)
