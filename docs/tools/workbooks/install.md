---
title: Requisitos e instalação de pastas de trabalho
description: Este documento descreve como baixar e instalar o Xamarin Workbooks, discutindo as plataformas com suporte e requisitos do sistema.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: b1303f21225d3ae7b7d3a796e4845afbfe554a22
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667705"
---
# <a name="workbooks-installation-and-requirements"></a>Requisitos e instalação de pastas de trabalho

<a name="install" />

## <a name="download-and-install"></a>Baixe e instale

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Verifique as [requisitos de](#requirements) abaixo.
2. Baixe e instale [pastas de trabalho do Xamarin para Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Inicie [brincar](~/tools/workbooks/workbook.md) com pastas de trabalho ou experimente o [exemplos](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Verifique as [requisitos de](#Requirements) abaixo.
2. Baixe e instale [Xamarin Workbooks para Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Inicie [brincar](~/tools/workbooks/workbook.md) com pastas de trabalho ou experimente o [exemplos](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 ou superior
- **Windows** -Windows 7 ou superior (com o Internet Explorer 11 ou superior e o .NET 4.6.1 ou superior)

#### <a name="supported-app-platforms"></a>Plataformas com suporte do aplicativo

|Plataforma de aplicativo|Suporte do sistema operacional|Observações|
|--- |--- |--- |
|Mac|Só tem suporte no Mac|
|iOS|Com suporte no Mac e Windows|Xamarin. IOS 11.0 e Xcode 9.0 ou posterior devem ser instalado no Mac. A execução de pastas de trabalho do iOS no Windows requer um host de build do Mac em execução a todos os itens acima e o [simulador de iOS remoto](~/tools/ios-simulator/index.md) instalado no Windows.|
|Android|Com suporte no Mac e Windows|Deve usar o emulador do Google, o Visual Studio ou Xamarin Android, com um dispositivo virtual > = 5.0|
|WPF|Só tem suporte no Windows|
|Console (.NET Framework)|Com suporte no Mac e Windows|
|Console (.NET Core)|Com suporte no Mac e Windows|


## <a name="reporting-bugs"></a>Relatório de Bugs

Por favor [relatar problemas no GitHub][bugs]e incluem todas as informações a seguir:

### <a name="log-files"></a>Arquivos de log

Sempre anexe arquivos de log do cliente de pastas de trabalho:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4 também apresenta a capacidade de selecionar o arquivo de log no Finder (macOS) ou o Explorer (Windows) diretamente no menu principal:

- **Ajuda > revelar o arquivo de Log**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Caminhos de log para pastas de trabalho 1.3 e anterior:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informações de versão de plataforma

Ele é muito útil saber detalhes sobre seu sistema operacional e os produtos Xamarin instalados.

No menu principal em pastas de trabalho:

* **Ajuda > Copiar informações de versão**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instruções para pastas de trabalho 1.3 e anterior:

O Visual Studio para Mac

- **Visual Studio > sobre o Visual Studio > Mostrar detalhes > Copiar informações**
- Cole no relatório de bugs

Visual Studio

- **Ajuda > sobre o Visual Studio > Copiar informações**
- Queremos sabe sua versão do sistema operacional e se você estiver executando o Windows de 32 bits ou 64 bits.

### <a name="samples"></a>Exemplos

Se você pode anexar ou vincular a **.workbooks** arquivo que você está tendo problemas com, que pode ajudar a resolver o bug mais rapidamente.

### <a name="devices"></a>Dispositivos

Se você estiver tendo problemas para se conectar a seu iOS ou uma pasta de trabalho do Android e já verificou [nossa página de solução de problemas](~/tools/workbooks/troubleshooting/index.md), precisaremos saber:

- Nome do dispositivo que você está tentando se conectar a
- Versão do sistema operacional do dispositivo
- Android: Verifique se que você está usando um x86 emulator
- Android: A plataforma de emulador que você está usando? Emulador do Google?
  Emulador do Android do Visual Studio? Xamarin Android Player?
- iOS no Windows: Qual versão do iOS Xamarin Remote Simulator você tenha instalado (Verifique **adicionar ou remover programas** na **painel de controle**)?
- iOS no Windows: Também forneça informações de versão de plataforma para o host de build do Mac
- O dispositivo tem conectividade de rede (seleção por meio do navegador da web)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependendo de como você adquiriu as pastas de trabalho, talvez você precise executar dois procedimentos de desinstalação. Verifique se ambos como desinstalar completamente o software.

#### <a name="visual-studio-installer"></a>Instalador do Visual Studio

Se você tiver o Visual Studio 2017, abra **instalador do Visual Studio**e procure **componentes individuais** para **Xamarin Workbooks**. Se estiver marcada, desmarque-a e, em seguida, clique em **modificar** para desinstalar.

#### <a name="system-uninstall"></a>Desinstalação do sistema

Se você instalou as pastas de trabalho por conta própria com um instalador baixado, ele precisará ser desinstalado usando o **aplicativos e recursos** página de configurações do sistema no Windows 10 ou por meio **Adicionar/remover programas** no controle Painel em versões anteriores do Windows.

> **Iniciar > Configurações > sistema > aplicativos e recursos**

![](install-images/windows-remove.png "Xamarin Workbooks conforme listado em &quot;aplicativos &amp; recursos&quot;")

**Você ainda deve seguir o procedimento para o instalador do Visual Studio garantir que as pastas de trabalho não obter reinstalado sem seu conhecimento.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

Começando com [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin Workbooks podem ser desinstalado de um terminal executando:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

O desinstalador detalha os arquivos e diretórios, ele removerá e solicite uma confirmação antes de continuar.

Passe o `-help` argumento para o `uninstall` script para cenários mais avançados.

Para versões mais antigas, será necessário remover manualmente o seguinte:

1. Exclua o aplicativo do Workbooks em `"/Applications/Xamarin Workbooks.app"`
2. Exclua o aplicativo do Inspector em `"Applications/Xamarin Inspector.app"`
2. Exclua os suplementos: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` e `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. Exclua o Inspector e os arquivos de suporte aqui: `/Library/Frameworks/Xamarin.Interactive.framework` e `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Fazer downgrade

O identificador de pacote para **aplicativos/Xamarin Workbooks.app** alterado de `com.xamarin.Inspector` para `com.xamarin.Workbooks` na versão 1.4, como pastas de trabalho e o Inspetor agora estão totalmente divididas.

Devido a um bug em instaladores mais antigos, não é possível fazer o downgrade de versões 1.4 ou mais recentes usando o 1.3.2 ou instaladores mais antigos.

Para fazer o downgrade de 1,4 ou mais recente para 1.3.2 ou mais antiga:

1. [Desinstalar o Workbooks e Inspector manualmente](#uninstall-macos)
2. Execute o 1.3.2 ou mais antigo `.pkg` installer
