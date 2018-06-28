---
title: Requisitos e instalação de Inspetor
description: Este documento descreve como instalar o Inspetor de Xamarin e discute o sistema operacional com suporte, IDEs e plataformas de aplicativo.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: 690329aa1577c66b3aa2794342a8e367477d3a74
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066917"
---
# <a name="inspector-installation-and-requirements"></a>Requisitos e instalação de Inspetor

## <a name="download-and-installation"></a>Download e instalação

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Baixe e instale o [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) e selecione o **desenvolvimento móvel com o .NET** carga de trabalho.
1. [Entrar](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) para ativar sua assinatura do Enterprise.
1. [Inspecionar](~/tools/inspector/inspect.md) seu próprio aplicativo!

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Baixe e instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Entrar](https://docs.microsoft.com/visualstudio/mac/activation) para ativar sua assinatura do Enterprise.
1. [Inspecionar](~/tools/inspector/inspect.md) seu próprio aplicativo!

-----

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 ou maior
- **Windows** -Windows 7 ou superior (com o Internet Explorer 11 ou superior e o .NET 4.6.1 ou posterior)

### <a name="supported-ides"></a>IDEs com suporte

- Visual Studio para Mac
- Visual Studio de 2017 com **desenvolvimento móvel com o .NET** carga de trabalho

Inspeção de aplicativo em tempo real está disponível para os clientes corporativos.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas com suporte do aplicativo

|Plataforma de aplicativos|Suporte IDE|Observações|
|--- |--- |--- |
|Mac|Só tem suporte no Visual Studio para Mac|
|iOS|Com suporte no Visual Studio de 2017 e o Visual Studio para Mac| |
|Android|Com suporte no Visual Studio de 2017 e o Visual Studio para Mac|Devem ter como destino Android > = 4.0.3, com **fastdev** habilitado.<br />Deve usar o Google, o Visual Studio ou o Xamarin Android emuladores. Android 7 emuladores não podem permitir a inspeção neste momento.|
|WPF|Só tem suporte no Visual Studio de 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Relatório de Bugs

Bugs devem ser relatados diretamente por meio do Visual Studio:

- **Ajuda > Enviar Comentários > relatar um problema**

Inclua todas as informações a seguir:

### <a name="platform-version-information"></a>Informações de versão de plataforma

Essas informações são essenciais.

O Visual Studio para Mac

- **O Visual Studio > sobre o Visual Studio > Mostrar detalhes > Copiar informações**
- Cole o relatório de erros

Visual Studio

- **Ajuda > sobre o Visual Studio > Copiar informações**
- Queremos sabe sua versão do sistema operacional e se você estiver executando o Windows de 32 bits ou 64 bits.

### <a name="log-files"></a>Arquivos de log

Sempre anexe IDE e o Inspetor de arquivos de log do cliente.

Cliente de Inspetor

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4 também inclui a capacidade de selecionar o arquivo de log no localizador (macOS) ou no Explorer (Windows) diretamente no menu principal:

- **Ajuda > revelar o arquivo de Log**

O Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- O conteúdo do Visual Studio **saída** painel pode também ser informativo.

### <a name="project-settings"></a>Configurações de projeto

Se você pode anexar o **. csproj** para o projeto que você está tentando inspecionar, seria extremamente úteis. Isso é mais fácil do que perguntando sobre configurações individuais.

Confirme também que você está na configuração de depuração.

### <a name="selected-devices"></a>Dispositivos selecionados

Para Android e iOS, é essencial que sabemos que dispositivo você está depurando quando deseja inspecionar. Precisamos saber:

- Nome do dispositivo, conforme mostrado em seu IDE
- Versão do sistema operacional do dispositivo
- Android: Verifique se você está usando uma arquitetura x86 emulador
- Android: A plataforma de emulador que você está usando? Emulador do Google? Emulador do Android do Visual Studio? Player de Xamarin Android?
- O aplicativo que você está depurando corretamente aparecem e funcionam no dispositivo?
- O dispositivo tem conectividade de rede (seleção por meio do navegador da web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
