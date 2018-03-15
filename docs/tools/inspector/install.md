---
title: "Requisitos e instalação de Inspetor"
description: Como baixar, instalar e usar o Inspetor de Xamarin.
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: a2e6f254c77ac099b5700543db5763b8bbb44fef
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="inspector-installation-and-requirements"></a>Requisitos e instalação de Inspetor

## <a name="download-and-installation"></a>Download e instalação


# <a name="windowstabvswin"></a>[Windows](#tab/vswin)

1. Baixe e instale o [pastas de trabalho do Xamarin & Inspetor para Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
2. [Inspecione o seu próprio aplicativo!](~/tools/inspector/inspect.md)

# <a name="macostabvsmac"></a>[macOS](#tab/vsmac)

1. Baixe e instale o [pastas de trabalho do Xamarin & Inspetor para Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
2. [Inspecione o seu próprio aplicativo!](~/tools/inspector/inspect.md)

-----

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 ou maior
- **Windows** -Windows 7 ou superior (com o Internet Explorer 11 ou superior e o .NET 4.6.1 ou posterior)

### <a name="supported-ides"></a>IDEs com suporte

- Xamarin Studio 6.2 ou posterior
- O Visual Studio para Mac Preview 4 ou superior
- Visual Studio 2015 com Xamarin 4.3. x ou superior
- Visual Studio de 2017 com carga de trabalho do Xamarin

Inspeção de aplicativo em tempo real está disponível para os clientes corporativos.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas com suporte do aplicativo

|Plataforma de aplicativos|Suporte IDE|Observações|
|--- |--- |--- |
|Mac (unificado)|Só tem suporte no Mac|
|iOS (Unified)|Com suporte no Visual Studio e XS|Inspecionar os aplicativos do iOS do Windows requer a mesma versão do Inspetor também sejam instalados no host de compilação de Mac.|
|Android|Com suporte no Visual Studio e XS|Devem ter como destino Android > = 4.0.3, com **fastdev** habilitado.<br />Deve usar o Google, o Visual Studio ou o Xamarin Android emuladores. Android 7 emuladores não podem permitir a inspeção neste momento.|
|WPF|Só tem suporte no Visual Studio no Windows|


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

Xamarin Studio

- **Xamarin Studio > sobre o Xamarin Studio > Mostrar detalhes > Copiar informações**
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

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

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
- Android: A plataforma de emulador que você está usando? Google Emulator? Emulador do Android do Visual Studio? Player de Xamarin Android?
- O aplicativo que você está depurando corretamente aparecem e funcionam no dispositivo?
- O dispositivo tem conectividade de rede (seleção por meio do navegador da web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependendo de como você adquiriu & Inspetor de pastas de trabalho, você terá que executar dois procedimentos de desinstalação. Verifique se ambos para desinstalar completamente o software.

#### <a name="visual-studio-installer"></a>Instalador do Visual Studio

Se você tiver o Visual Studio de 2017, abra **instalador do Visual Studio**e examinar **componentes individuais** para **pastas de trabalho do Xamarin**. Se estiver marcada, desmarque-a e, em seguida, clique em "Modificar" para desinstalar.

#### <a name="system-uninstall"></a>Desinstalação do sistema

Se você instalou & Inspetor de pastas de trabalho por conta própria com um instalador baixado, ela precisará ser desinstalado usando o **aplicativos e recursos** página de configurações do sistema no Windows 10 ou via **adicionar ou remover programas**no painel de controle em versões anteriores do Windows.

> **Iniciar > Configurações > sistema > aplicativos e recursos**

![](install-images/windows-remove.png "Pastas de trabalho do Xamarin e Inspetor conforme listado no 'Aplicativos e recursos'")

**Você ainda deve seguir o procedimento para o instalador do Visual Studio certificar-se de pastas de trabalho & Inspetor não obter reinstalado sem seu conhecimento.**

### <a name="macos"></a>macOS

Começando com [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), pastas de trabalho do Xamarin & Inspetor podem ser desinstalados de um terminal executando:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

O desinstalador detalha os arquivos e diretórios, ele removerá e solicite uma confirmação antes de continuar.

Passar o `-help` argumento para o `uninstall` script para cenários mais avançados.

Para versões mais antigas, será necessário remover manualmente o seguinte:

1. Exclua o aplicativo do Workbooks em `"/Applications/Xamarin Workbooks.app"`
2. Exclua o aplicativo do Inspector em `"Applications/Xamarin Inspector.app"`
2. Exclua os suplementos: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` e `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Exclua o Inspector e os arquivos de suporte aqui: `/Library/Frameworks/Xamarin.Interactive.framework` e `/Library/Frameworks/Xamarin.Inspector.framework`

