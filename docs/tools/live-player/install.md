---
title: Configuração do Xamarin Live Player para Visual Studio
description: Este documento descreve como usar o Xamarin Live Player para fazer edições ao vivo em um aplicativo em execução.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: a29a637526c2829b44ae89d505dac37a648dee77
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157745"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Configuração do Xamarin Live Player para Visual Studio

![Versão prévia do recurso](~/media/shared/preview.png)

> [!WARNING]
> O Xamarin Live Player visualização foi encerrada. O aplicativo não está mais disponível. As instruções a seguir são fornecidas para clientes que continuam a usar a visualização com o Visual Studio 2017.

> [!TIP]
> Você pode usar o [XAML pré-visualizador](~/xamarin-forms/xaml/xaml-previewer/index.md) no 2019 do Visual Studio ou Visual Studio para Mac para exibir seus designs de tela, conforme você editá-los.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Usando o Xamarin Live Player

Você já deve ter o aplicativo Xamarin Live Player no seu dispositivo. Não está disponível para download.

1. Abra **Visual Studio 2017**.
2. Vá para **Ferramentas > Opções...**  e selecione o **Xamarin > outros** guia.
3. Escala **habilitar Xamarin Live Player**:

    ![Marque a caixa Habilitar Xamarin Live Player na janela Opções](install-images/vs2017-options.png)

4. Criar ou abrir um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
5. Escolher **Live Player** na lista de dispositivos:

    ![Lista de dispositivos inclui uma opção do Xamarin Live Player](install-images/devices-empty-windows.png)

    - Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
    - Caso contrário, você será solicitado a emparelhar um dispositivo quando necessário.

6. Pressione a **executados** botão, ou selecione uma das seguintes opções da **executar** ou menu de atalho:

    - **Iniciar sem depuração** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo (o aplicativo é reiniciado, pois as alterações são feitas e o arquivo salvo).
    - **Iniciar depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas não é possível editar o código.

    Como alternativa, selecione **Ferramentas > Xamarin Live Player > Live exibição atual da execução**, que permite que você editar o aplicativo e ver as alterações ocorrem no dispositivo. O modo de exibição atual é mostrado (em vez da tela do aplicativo principal).

7. Se um dispositivo já está emparelhado e o aplicativo Xamarin Live Player está em execução no dispositivo, o código será executado imediatamente!

    Se nenhum dispositivo está emparelhado, um código QR será exibida com instruções sobre como emparelhar um dispositivo:

    ![Par de uma janela de dispositivo](install-images/manage-empty-windows.png)

    Se o dispositivo não pode ser contatado para emparelhamento, pode aparecer um erro.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Usando o Xamarin Live Player

Você já deve ter o aplicativo Xamarin Live Player no seu dispositivo. Não está disponível para download.

1. Abra **Visual Studio para Mac**.
2. Vá para **Visual Studio > Preferências...**  e selecione o **projetos > Xamarin Live Player (versão prévia)** guia.
3. Escala **habilitar Xamarin Live Player**:

    [![Marque a caixa Habilitar Xamarin Live Player na janela Opções](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Criar ou abrir um projeto do Xamarin (ou um [exemplo](~/tools/live-player/samples.md)).
5. Escolher **Live Player** na lista de dispositivos.

    ![Lista de dispositivos inclui uma opção do Xamarin Live Player](install-images/devices.png)

    - Se você já tiver emparelhado um dispositivo, ele estará disponível como uma opção.
    - Caso contrário, você será solicitado a emparelhar um dispositivo quando necessário.
    - Escolha **dispositivos do Xamarin Live Player...**  para gerenciar os dispositivos que você deseja usar com o Xamarin Live Player.

6. Pressione a **executados** botão, ou selecione uma das seguintes opções da **executar** ou menu de atalho:

    ![Opções de menu de execução](install-images/run-menu.png)

    - **Iniciar sem depuração** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo (o aplicativo é reiniciado, pois as alterações são feitas e o arquivo salvo).
    - **Iniciar depuração** – você pode definir pontos de interrupção e inspecionar variáveis, mas não é possível editar o código.
    - **Exibição atual da execução dinâmica** – você pode editar o aplicativo e ver as alterações ocorrem no dispositivo. O modo de exibição atual é mostrado (em vez da tela do aplicativo principal).

7. Se um dispositivo já está emparelhado e o aplicativo Xamarin Live Player está em execução no dispositivo, o código será executado imediatamente!

    Se nenhum dispositivo está emparelhado, um código QR será exibida com instruções sobre como emparelhar um dispositivo:

    ![Par de uma janela de dispositivo](install-images/manage-empty.png)

    Se o dispositivo não pode ser contatado para emparelhamento, um erro será exibida:

    ![Não é possível conectar-se a mensagem de erro de dispositivo](install-images/error-cannot-connect.png)

-----

Se você tiver problemas ou não pode se conectar, consulte [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
