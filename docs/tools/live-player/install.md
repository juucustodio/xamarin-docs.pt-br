---
title: O programa de instalação do Xamarin Live Player
description: Este documento descreve como configurar o Xamarin Live Player e usá-lo para fazer edições ao vivo em um aplicativo em execução.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: topgenorth
ms.author: toopge
ms.date: 05/14/2018
ms.openlocfilehash: 40c03e978cd9ce4666089f1b2a1e2ee8f47dbd81
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831667"
---
# <a name="xamarin-live-player-setup"></a>O programa de instalação do Xamarin Live Player

Xamarin Live Player permite que você faça edições dinâmicas para seu aplicativo e tem essas alterações refletidas em tempo real no dispositivo. O código é executado dentro do aplicativo Xamarin Live Player – sem necessidade de configurar os emuladores ou use cabos para implantar! Este artigo descreve como configurar o Xamarin Live Player.

![Recurso de visualização](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1. Obtenha o aplicativo

# <a name="androidtabandroid"></a>[Android](#tab/android)

Xamarin Live Player está disponível para Android da Google Play:

[ ![Disponível no Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Para dispositivos Android sem Google Play, o Xamarin Live Player está disponível por meio [HockeyApp](https://aka.ms/xlp-hockeyapp) distribuição. Além disso, a visualização prévia compila para o Android pode ser instalado diretamente no Google Play ao optar ao [programa beta aberto](https://play.google.com/apps/testing/com.xamarin.live)

# <a name="iostabios"></a>[iOS](#tab/ios)

Recomendamos que os usuários ingressem no aplicativo Xamarin Live Player iOS visualização desfrutar do acesso rápido para os aprimoramentos mais recentes por meio do TestFlight. Acessando o Xamarin Live Player, você está concordando com a Microsoft [termos de uso](https://www.microsoft.com/en-us/legal/intellectualproperty/copyright/default.aspx) & [Privacy Statement](https://privacy.microsoft.com/en-us/privacystatement). Microsoft pode usar suas informações de contato para fornecer atualizações e ofertas especiais sobre o Xamarin e outros produtos e serviços Microsoft. Você pode cancelar a assinatura a qualquer momento.

Para acessar o Xamarin Live Player iOS versão prévia, conclua o [informações de registro de TestFlight](https://fastring.xamarinliveplayer.com/), após o qual você receberá um email de TestFlight sobre como instalar o Xamarin Live Player iOS versão prévia.

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="2-get-visual-studio-2017"></a>2. Obtenha o Visual Studio 2017

Requer o Xamarin Live Player:

- Visual Studio 2017 15.4 ou mais recente.
- Um computador com Visual Studio e um dispositivo na mesma rede Wi-Fi.

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Usando o Xamarin Live Player pela primeira vez

1. Abra **Visual Studio 2017**.
2. Vá para **Ferramentas > Opções...**  e selecione o **Xamarin > outros** guia.
3. Escala **habilitar Xamarin Live Player**:

  ![Marque a caixa Habilitar Xamarin Live Player na janela Opções](install-images/vs2017-options.png)

2. Criar ou abrir um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
3. Escolher **Live Player** na lista de dispositivos:

  ![Lista de dispositivos inclui uma opção do Xamarin Live Player](install-images/devices-empty-windows.png)

  * Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
  * Caso contrário, você será solicitado a emparelhar um dispositivo quando necessário.
4. Pressione a **executados** botão, ou selecione uma das seguintes opções da **executar** ou menu de atalho:

  - **Iniciar sem depuração** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo (o aplicativo é reiniciado, pois as alterações são feitas e o arquivo salvo).
  - **Iniciar depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas não é possível editar o código.

  Como alternativa, selecione **Ferramentas > Xamarin Live Player > Live exibição atual da execução**, que permite que você editar o aplicativo e ver as alterações ocorrem no dispositivo. O modo de exibição atual é mostrado (em vez da tela do aplicativo principal).

5. Se um dispositivo já está emparelhado e o aplicativo Xamarin Live Player está em execução no dispositivo, o código será executado imediatamente!

  Se nenhum dispositivo está emparelhado, aparecerá um código QR com instruções sobre como emparelhar um dispositivo:

  ![Par de uma janela de dispositivo](install-images/manage-empty-windows.png)

  Se o dispositivo não pode ser contatado para emparelhamento, pode aparecer um erro.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="2-get-visual-studio-for-mac"></a>2. Obtenha o Visual Studio para Mac

Requer o Xamarin Live Player:

- OS X 10.11, macOS 10.12 ou superior
- Visual Studio para Mac
- Um Mac e um dispositivo na mesma rede Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Usando o Xamarin Live Player pela primeira vez

1. Abra **Visual Studio para Mac**.
2. Vá para **Visual Studio > Preferências...**  e selecione o **projetos > Xamarin Live Player (versão prévia)** guia.
3. Escala **habilitar Xamarin Live Player**:

  [![Marque a caixa Habilitar Xamarin Live Player na janela Opções](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

2. Criar ou abrir um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
3. Escolher **Live Player** na lista de dispositivos.

  ![Lista de dispositivos inclui uma opção do Xamarin Live Player](install-images/devices.png)

  * Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
  * Caso contrário, você será solicitado a emparelhar um dispositivo quando necessário.
  * Escolha **dispositivos do Xamarin Live Player...**  para gerenciar os dispositivos que você deseja usar com o Xamarin Live Player.

4. Pressione a **executados** botão, ou selecione uma das seguintes opções da **executar** ou menu de atalho:

  ![Opções de menu de execução](install-images/run-menu.png)

  - **Iniciar sem depuração** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo (o aplicativo é reiniciado, pois as alterações são feitas e o arquivo salvo).
  - **Iniciar depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas não é possível editar o código.
  - **Exibição atual da execução dinâmica** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo. O modo de exibição atual é mostrado (em vez da tela do aplicativo principal).

5. Se um dispositivo já está emparelhado e o aplicativo Xamarin Live Player está em execução no dispositivo, o código será executado imediatamente!

  Se nenhum dispositivo está emparelhado, um código QR será exibida com instruções sobre como emparelhar um dispositivo:

  ![Par de uma janela de dispositivo](install-images/manage-empty.png)

  Se o dispositivo não pode ser contatado para emparelhamento, um erro será exibida:

  ![Não é possível conectar-se a mensagem de erro de dispositivo](install-images/error-cannot-connect.png)


-----

Se você tiver problemas ou não pode se conectar, consulte [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).


## <a name="related-links"></a>Links relacionados

- [Limitações](~/tools/live-player/limitations.md)
- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Exemplos do Xamarin Live Player](~/tools/live-player/samples.md)
