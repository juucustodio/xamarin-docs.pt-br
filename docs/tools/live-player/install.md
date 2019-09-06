---
title: Configuração do Xamarin Live Player Visual Studio
description: Este documento descreve como usar o Xamarin Live Player para fazer edições ao vivo em um aplicativo em execução.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: conceptdev
ms.author: crdun
ms.date: 06/13/2019
ms.openlocfilehash: 94f1d36bf97aab7eabb57e6f2712c9850b390ab1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290481"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Configuração do Xamarin Live Player Visual Studio

![Versão prévia do recurso](~/media/shared/preview.png)

> [!WARNING]
> A visualização do Xamarin Live Player foi encerrada. O aplicativo não está mais disponível. As instruções a seguir são fornecidas para os clientes que continuam a usar a visualização com o Visual Studio 2017.

> [!TIP]
> Você pode usar o [Visualizar XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) no Visual Studio 2019 ou Visual Studio para Mac para exibir os designs de tela ao editá-los.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Usando Xamarin Live Player

Você já deve ter o aplicativo Xamarin Live Player em seu dispositivo. Ele não está mais disponível para download.

1. Abra o **Visual Studio 2017**.
2. Vá para **ferramentas > opções...** e selecione o **Xamarin > outra** guia.
3. **Habilitação**de tique Xamarin Live Player:

    ![Marque a caixa habilitar Xamarin Live Player na janela Opções](install-images/vs2017-options.png)

4. Crie ou abra um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
5. Escolha **Live Player** na lista de dispositivos:

    ![A lista de dispositivos inclui uma opção Xamarin Live Player](install-images/devices-empty-windows.png)

    - Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
    - Caso contrário, será solicitado que você emparelhe um dispositivo quando necessário.

6. Pressione o botão **executar** ou selecione uma das seguintes opções no menu **executar** ou clique com o botão direito do mouse:

    - **Iniciar sem depuração** – você pode editar o aplicativo e ver que as alterações ocorrem no dispositivo (o aplicativo é reiniciado conforme as alterações são feitas e o arquivo salvo).
    - **Iniciar Depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas o código não pode ser editado.

    Como alternativa, selecione **ferramentas > Xamarin Live Player > execução dinâmica executar o modo de exibição atual**, que permite editar o aplicativo e ver as alterações ocorrem no dispositivo. A exibição atual é mostrada (em vez da tela principal do aplicativo).

7. Se um dispositivo já estiver emparelhado e o aplicativo Xamarin Live Player estiver em execução no dispositivo, o código será executado imediatamente!

    Se nenhum dispositivo for emparelhado, um código QR será exibido com instruções sobre como emparelhar um dispositivo:

    ![Emparelhar uma janela do dispositivo](install-images/manage-empty-windows.png)

    Se o dispositivo não puder ser contatado para emparelhamento, poderá aparecer um erro.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Usando Xamarin Live Player

Você já deve ter o aplicativo Xamarin Live Player em seu dispositivo. Ele não está mais disponível para download.

1. Abra **Visual Studio para Mac**.
2. Vá para o **Visual Studio > preferências...** e selecione a guia **projetos > Xamarin Live Player (versão prévia)** .
3. **Habilitação**de tique Xamarin Live Player:

    [![Marque a caixa habilitar Xamarin Live Player na janela Opções](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Crie ou abra um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
5. Escolha **Live Player** na lista de dispositivos.

    ![A lista de dispositivos inclui uma opção Xamarin Live Player](install-images/devices.png)

    - Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
    - Caso contrário, será solicitado que você emparelhe um dispositivo quando necessário.
    - Escolha **Xamarin Live Player dispositivos...** para gerenciar os dispositivos que você deseja usar com Xamarin Live Player.

6. Pressione o botão **executar** ou selecione uma das seguintes opções no menu **executar** ou clique com o botão direito do mouse:

    ![Opções do menu executar](install-images/run-menu.png)

    - **Iniciar sem depuração** – você pode editar o aplicativo e ver que as alterações ocorrem no dispositivo (o aplicativo é reiniciado conforme as alterações são feitas e o arquivo salvo).
    - **Iniciar Depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas o código não pode ser editado.
    - **Modo de exibição atual da execução dinâmica** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo. A exibição atual é mostrada (em vez da tela principal do aplicativo).

7. Se um dispositivo já estiver emparelhado e o aplicativo Xamarin Live Player estiver em execução no dispositivo, o código será executado imediatamente!

    Se nenhum dispositivo for emparelhado, um código QR será exibido com instruções sobre como emparelhar um dispositivo:

    ![Emparelhar uma janela do dispositivo](install-images/manage-empty.png)

    Se o dispositivo não puder ser contatado para emparelhamento, um erro será exibido:

    ![Não é possível se conectar à mensagem de erro do dispositivo](install-images/error-cannot-connect.png)

-----

Se você tiver problemas ou não puder se conectar, consulte [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
