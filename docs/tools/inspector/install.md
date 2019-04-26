---
title: Requisitos e instalação do Inspetor
description: Este documento descreve como instalar o Xamarin Inspector e discute o sistema operacional com suporte, IDEs e plataformas de aplicativo.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2bbf0bda42b7bce483d9d036ebf39314dcb73072
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373463"
---
# <a name="inspector-installation-and-requirements"></a>Requisitos e instalação do Inspetor

## <a name="download-and-installation"></a>Download e instalação

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Baixe e instale [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) e selecione o **desenvolvimento móvel com .NET** carga de trabalho.
1. [Entrar](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) para ativar sua assinatura Enterprise.
1. [Inspecionar](~/tools/inspector/inspect.md) seu próprio aplicativo!

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Baixe e instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Entrar](https://docs.microsoft.com/visualstudio/mac/activation) para ativar sua assinatura Enterprise.
1. [Inspecionar](~/tools/inspector/inspect.md) seu próprio aplicativo!

-----

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 ou superior
- **Windows** -Windows 7 ou superior (com o Internet Explorer 11 ou superior e o .NET 4.6.1 ou superior)

### <a name="supported-ides"></a>IDEs com suporte

- Visual Studio para Mac
- Visual Studio 2017 com **desenvolvimento móvel com .NET** carga de trabalho

Inspeção de aplicativo em tempo real está disponível para clientes empresariais.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas com suporte do aplicativo

|Plataforma de aplicativo|Suporte ao IDE|Observações|
|--- |--- |--- |
|Mac|Só tem suporte no Visual Studio para Mac|
|iOS|Com suporte no Visual Studio 2017 e Visual Studio para Mac| Comportamento do vinculador deve ser definido como **não vincular** (sob **Build do iOS** opções do projeto) |
|Android|Com suporte no Visual Studio 2017 e Visual Studio para Mac|Deve ter como destino Android > = 4.0.3, com **fastdev** habilitado.<br />Deve usar os emuladores do Google, o Visual Studio ou o Xamarin Android. Emuladores do Android 7 podem não permitir a inspeção neste momento.|
|WPF|Só tem suporte no Visual Studio 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Relatório de Bugs

Bugs devem ser relatados diretamente por meio do Visual Studio:

- **Ajuda > Enviar Comentários > relatar um problema**

Inclua todas as informações a seguir:

### <a name="platform-version-information"></a>Informações de versão de plataforma

Essas informações são essenciais.

O Visual Studio para Mac

- **Visual Studio > sobre o Visual Studio > Mostrar detalhes > Copiar informações**
- Cole no relatório de bugs

Visual Studio

- **Ajuda > sobre o Visual Studio > Copiar informações**
- Queremos sabe sua versão do sistema operacional e se você estiver executando o Windows de 32 bits ou 64 bits.

### <a name="log-files"></a>Arquivos de log

Sempre anexe IDE e o Inspetor de arquivos de log do cliente.

Cliente de Inspetor

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4 também apresenta a capacidade de selecionar o arquivo de log no Finder (macOS) ou o Explorer (Windows) diretamente no menu principal:

- **Ajuda > revelar o arquivo de Log**

O Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- O conteúdo do Visual Studio **saída** painel também pode ser informativo.

### <a name="project-settings"></a>Configurações do projeto

Se você pode anexar a **. csproj** para o projeto que você está tentando inspecionar, seria extremamente útil. Isso é mais fácil do que fazer sobre as configurações individuais.

Confirme também que você está na configuração de depuração.

### <a name="selected-devices"></a>Dispositivos selecionados

Para Android e iOS, é vital que sabemos qual dispositivo você está depurando quando você deseja inspecionar. Precisamos saber:

- Nome do dispositivo, conforme mostrado no seu IDE
- Versão do sistema operacional do dispositivo
- Android: Verifique se que você está usando um x86 emulator
- Android: A plataforma de emulador que você está usando? Emulador do Google? Emulador do Android do Visual Studio? Xamarin Android Player?
- O aplicativo que você está depurando corretamente aparecer e o dispositivo de função?
- O dispositivo tem conectividade de rede (seleção por meio do navegador da web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
