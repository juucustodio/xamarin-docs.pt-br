---
title: Instalação e requisitos de pastas de trabalho
description: Este documento descreve como baixar e instalar o Xamarin Workbooks, discutindo as plataformas com suporte e os requisitos do sistema.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: d94a14bcd1486b7b64e9ce6d9fee6d36406b2710
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511851"
---
# <a name="workbooks-installation-and-requirements"></a>Instalação e requisitos de pastas de trabalho

<a name="install" />

## <a name="download-and-install"></a>Baixar e instalar

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Verifique os [requisitos](#requirements) abaixo.
2. Baixe e instale o [Xamarin Workbooks para Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Comece a [brincar](~/tools/workbooks/workbook.md) com pastas de trabalho.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Verifique os [requisitos](#requirements) abaixo.
2. Baixe e instale o [Xamarin Workbooks para Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Comece a [jogar](~/tools/workbooks/workbook.md).

-----

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10,11 ou superior
- **Windows** -Windows 7 ou superior (com o Internet Explorer 11 ou superior e o .NET 4.6.1 ou superior)

#### <a name="supported-app-platforms"></a>Plataformas de aplicativos com suporte

|Plataforma de aplicativo|Suporte do so|Observações|
|--- |--- |--- |
|Mac|Somente com suporte no Mac|
|iOS|Com suporte no Mac e no Windows|O Xamarin. iOS 11,0 e o Xcode 9,0 ou posterior devem ser instalados no Mac. A execução de pastas de trabalho do iOS no Windows requer um host de Build do Mac executando todas as versões acima e o simulador do [Ios remoto](~/tools/ios-simulator/index.md) instalado no Windows.|
|Android|Com suporte no Mac e no Windows|Deve usar o Google, o Visual Studio ou o emulador do Xamarin Android, com um dispositivo virtual > = 5,0|
|WPF|Somente com suporte no Windows|
|Console (.NET Framework)|Com suporte no Mac e no Windows|
|Console (.NET Core)|Com suporte no Mac e no Windows|


## <a name="reporting-bugs"></a>Relatando bugs

[Informe os problemas no GitHub][bugs]e inclua todas as seguintes informações:

### <a name="log-files"></a>Arquivos de log

Sempre anexar arquivos de log do cliente de pastas de trabalho:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

o 1.4. x também apresenta a capacidade de selecionar o arquivo de log no Finder (Mac) ou Explorer (Windows) diretamente no menu principal:

- **Ajuda > revelar arquivo de log**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Caminhos de log das pastas de trabalho 1,3 e anteriores:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informações de versão da plataforma

É muito útil saber detalhes sobre o seu sistema operacional e instalar os produtos Xamarin.

No menu principal em pastas de trabalho:

* **Ajuda > copiar informações de versão**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instruções para as pastas de trabalho 1,3 e anteriores:

Visual Studio para Mac

- **O Visual Studio > sobre o Visual Studio > Mostrar detalhes > copiar informações**
- Colar no relatório de bugs

Visual Studio

- **Ajuda > sobre o Visual Studio > copiar informações**
- Informe-nos a sua versão do sistema operacional e se você está executando o Windows de 32 bits ou 64 bits.

### <a name="samples"></a>Exemplos

Se você puder anexar ou vincular ao arquivo **. Workbooks** com problemas, isso pode ajudar a resolver seu bug mais rapidamente.

### <a name="devices"></a>Dispositivos

Se você estiver tendo problemas para conectar sua pasta de trabalho do iOS ou Android e já tiver marcado [nossa página de solução de problemas](~/tools/workbooks/troubleshooting/index.md), precisaremos saber:

- Nome do dispositivo ao qual você está tentando se conectar
- Versão do sistema operacional do seu dispositivo
- Android: Verifique se você está usando um emulador x86
- Android: Qual plataforma de emulador você está usando? O Google Emulator?
  Android Emulator do Visual Studio? Xamarin Android Player?
- iOS no Windows: Qual versão do Xamarin Remote iOS Simulator você instalou (Marque **Adicionar/remover programas** no **painel de controle**)?
- iOS no Windows: Forneça também informações de versão da plataforma para seu host de Build do Mac
- O dispositivo tem conectividade de rede (verifique via navegador da Web)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependendo de como você adquiriu as pastas de trabalho, talvez seja necessário executar dois procedimentos de desinstalação. Verifique ambos para desinstalar completamente o software.

#### <a name="visual-studio-installer"></a>Instalador do Visual Studio

Se você tiver o Visual Studio 2017, abra **instalador do Visual Studio**e procure por **Xamarin Workbooks** **componentes individuais** . Se estiver marcada, desmarque-a e clique em **Modificar** para desinstalar.

#### <a name="system-uninstall"></a>Desinstalação do sistema

Se você tiver instalado pastas de trabalho com um instalador baixado, ele precisará ser desinstalado por meio da página de configurações do sistema **aplicativos & recursos** no Windows 10 ou por meio de **Adicionar ou remover programas** no painel de controle em versões anteriores do Windows.

> **Iniciar Configurações de > > recursos do System > aplicativos &**

![](install-images/windows-remove.png "Xamarin Workbooks conforme listado em &quot;recursos &amp; de aplicativos&quot;")

**Você ainda deve seguir o procedimento para a Instalador do Visual Studio garantir que as pastas de trabalho não sejam reinstaladas sem o seu conhecimento.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partir do [1.2.2](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/interactive/interactive-1.2.md), Xamarin Workbooks pode ser desinstalado de um terminal executando:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

O desinstalador detalhará os arquivos e diretórios que serão removidos e solicitará a confirmação antes de continuar.

Passe o `-help` argumento para o `uninstall` script para cenários mais avançados.

Para versões mais antigas, será necessário remover manualmente o seguinte:

1. Exclua o aplicativo do Workbooks em `"/Applications/Xamarin Workbooks.app"`
2. Exclua o aplicativo do Inspector em `"Applications/Xamarin Inspector.app"`
3. Exclua os suplementos: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` e `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. Exclua o Inspector e os arquivos de suporte aqui: `/Library/Frameworks/Xamarin.Interactive.framework` e `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Downgrade

O identificador de pacote para **pastas de trabalho/Applications/Xamarin. app** foi `com.xamarin.Workbooks` alterado de `com.xamarin.Inspector` para na versão 1,4, já que as pastas de trabalho e o Inspetor agora estão totalmente divididos.

Devido a um bug em instaladores mais antigos, não é possível fazer downgrade de 1,4 ou versões mais recentes usando o 1.3.2 ou instaladores mais antigos.

Para fazer o downgrade de 1,4 ou mais recente para o 1.3.2 ou mais antigo:

1. [Desinstalar pastas de trabalho & Inspetor manualmente](#uninstall-macos)
2. Executar o instalador do 1.3.2 `.pkg` ou mais antigo
