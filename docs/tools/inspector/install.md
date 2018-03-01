---
title: "Instalação e requisitos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: a587935e35882ed1dc68817fbbe1ae3e91200f29
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="installation-and-requirements"></a>Instalação e requisitos

<script> var inspectorOnLoad = (função) {var primaryTextBase = "Pastas de trabalho do Xamarin & Inspetor para"; var secondaryTextBase = "ou baixe para"; var inspectorDownloadUrlMac = "https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; var inspectorDownloadUrlWin = "https://dl.xamarin.com/interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  if (/win/i.test(navigator.platform.toLowerCase())) { aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase; }

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + "Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + "Windows". };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

## <a name="download-and-installation"></a>Download e instalação

<ol>
  <li>Baixe e instale o <a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">& Inspetor para Mac de pastas de trabalho do Xamarin</a> (<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">ou de download para o Windows</a>).
  </li>
  <li><a href="~/tools/inspector/inspect.md"> Inspecione o seu próprio aplicativo!</a>
    </li>
</ol>

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

<table>
<thead>
  <tr>
    <th>Plataforma de aplicativos</th>
    <th>Suporte IDE</th>
    <th>Observações</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Mac (unificado)</td>
    <td>Só tem suporte no Mac</td>
    <td/>
  </tr>
  <tr>
    <td>iOS (Unified)</td>
    <td>Com suporte no Visual Studio e XS</td>
    <td>Inspecionar os aplicativos do iOS do Windows requer a mesma versão do Inspetor também sejam instalados no host de compilação de Mac.</td>
  </tr>
  <tr>
    <td>Android</td>
    <td>Com suporte no Visual Studio e XS</td>
    <td>
      <ul>
        <li>Devem ter como destino Android > = 4.0.3</li>
        <li>Deve ter fastdev habilitado</li>
        <li>Deve usar o Google, o Visual Studio ou o Xamarin Android emuladores. Android 7 emuladores não podem permitir a inspeção neste momento.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>Só tem suporte no Visual Studio no Windows</td>
    <td/>
  </tr>
</tbody>
</table>

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Relatório de Bugs

Bugs devem ser relatados diretamente por meio do Visual Studio:

- **Ajuda → enviar comentários → relatar um problema**

Inclua todas as informações a seguir:

### <a name="platform-version-information"></a>Informações de versão de plataforma

Essas informações são essenciais.

O Visual Studio para Mac

- **O Visual Studio → sobre informações de cópia do Visual Studio → Mostrar detalhes →**
- Cole o relatório de erros

Xamarin Studio

- **Xamarin Studio → sobre Xamarin Studio → Mostrar detalhes → copiar informações**
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

- **Arquivo de Log de revelação ajuda →**

O Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- O conteúdo do Visual Studio `Output` painel pode também ser informativo.

### <a name="project-settings"></a>Configurações de projeto

Se você pode anexar o `.csproj` para o projeto que você está tentando inspecionar, seria extremamente úteis. Isso é mais fácil do que perguntando sobre configurações individuais.

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

Se você tiver o Visual Studio de 2017, abra "Instalador do Visual Studio" e procure "Xamarin pastas de trabalho" em "Componentes individuais". Se estiver marcada, desmarque-a e, em seguida, clique em "Modificar" para desinstalar.

#### <a name="system-uninstall"></a>Desinstalação do sistema

Se você instalou & Inspetor de pastas de trabalho por conta própria com um instalador baixado, ela precisará ser desinstalado usando o **aplicativos e recursos** página de configurações do sistema no Windows 10 ou via **adicionar ou remover programas**no painel de controle em versões anteriores do Windows.

> **Iniciar → configurações → recursos e aplicativos de sistema →**

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

