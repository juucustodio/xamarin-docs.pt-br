---
title: "Instalação e requisitos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: dffea94b309b3c945a37a116668486f404f544b3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="installation-and-requirements"></a>Instalação e requisitos

<script> var inspectorOnLoad = (função) {var primaryTextBase = "Xamarin as pastas de trabalho para"; var secondaryTextBase = "ou baixe para"; var inspectorDownloadUrlMac = "https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; var inspectorDownloadUrlWin = " https://DL.xamarin.com/Interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  if (/win/i.test(navigator.platform.toLowerCase())) { aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase; }

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + "Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + "Windows". };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

<a name="install" />

## <a name="download-and-install"></a>Baixar e instalar

<ol>
  <li>Verifique o <a href="#Requirements"> requisitos</a> abaixo.</li>
  <li>Baixe e instale o <a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">Xamarin pastas de trabalho para Mac</a> (<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">ou de download para o Windows</a>).
  </li>
  <li>Iniciar <a href="~/tools/workbooks/workbook.md"> colocando</a> com pastas de trabalho ou experimentar o <a href="https://developer.xamarin.com/workbooks/">exemplos</a>.
    </li>
</ol>

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 ou maior
- **Windows** -Windows 7 ou superior (com o Internet Explorer 11 ou superior e o .NET 4.6.1 ou posterior)

#### <a name="supported-app-platforms"></a>Plataformas com suporte do aplicativo

<table>
<thead>
  <tr>
    <th>Plataforma de aplicativos</th>
    <th>Suporte de sistema operacional</th>
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
    <td>Suporte para Mac e Windows</td>
    <td>
      <ul>
        <li>Xamarin 11.0 e Xcode 9.0 ou posterior devem ser instalado no Mac.</li>
        <li>Executar o iOS pastas de trabalho no Windows requer um host de compilação de Mac executando todos os itens acima e o <a href="~/tools/ios-simulator.md">simulador de iOS remoto</a> instalado no Windows.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Android</td>
    <td>Suporte para Mac e Windows</td>
    <td>Deve usar o emulador do Google, o Visual Studio ou o Xamarin Android, com um dispositivo virtual > = 5.0</td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>Só tem suporte no Windows</td>
    <td/>
  </tr>
  <tr>
    <td>Console (.NET Framework)</td>
    <td>Suporte para Mac e Windows</td>
    <td/>
  </tr>
  <tr>
    <td>Console (.NET Core)</td>
    <td>Suporte para Mac e Windows</td>
    <td/>
  </tr>
</tbody>
</table>

## <a name="reporting-bugs"></a>Relatório de Bugs

Por favor, [relatar problemas no GitHub][bugs]e incluem todas as informações a seguir:

### <a name="log-files"></a>Arquivos de log

Sempre anexe arquivos de log do cliente de pastas de trabalho:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4 também inclui a capacidade de selecionar o arquivo de log no localizador (macOS) ou no Explorer (Windows) diretamente no menu principal:

- **Arquivo de Log de revelação ajuda →**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Caminhos para pastas de trabalho 1.3 e anterior do log:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informações de versão de plataforma

Ele é muito útil para saber detalhes sobre o sistema operacional e Xamarin produtos instalados.

No menu principal em pastas de trabalho:

* **Ajudar a informações de versão de cópia →**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instruções para pastas de trabalho 1.3 e anterior:

O Visual Studio para Mac

- **O Visual Studio → sobre informações de cópia do Visual Studio → Mostrar detalhes →**
- Cole o relatório de erros

Visual Studio

- **Ajuda → sobre o Visual Studio → copiar informações**
- Queremos sabe sua versão do sistema operacional e se você estiver executando o Windows de 32 bits ou 64 bits.

### <a name="samples"></a>Exemplos

Se você pode anexar ou vincular a `.workbooks` arquivo estiver com problemas, que pode ajudar a resolver o erro mais rapidamente.

### <a name="devices"></a>Dispositivos

Se você estiver tendo problemas para se conectar a seu iOS ou Android pasta de trabalho e verificou já [nossa página de solução de problemas](~/tools/workbooks/troubleshooting/index.md), vamos precisar saber:

- Nome do dispositivo que você está tentando se conectar a
- Versão do sistema operacional do dispositivo
- Android: Verifique se você está usando uma arquitetura x86 emulador
- Android: A plataforma de emulador que você está usando? Google Emulator?
  Emulador do Android do Visual Studio? Player de Xamarin Android?
- iOS no Windows: qual versão do iOS remoto Xamarin simulador você tenha instalado (Verifique `Add/Remove Programs` em `Control Panel`)?
- iOS no Windows:. forneça informações de versão de plataforma para o host de compilação do Mac
- O dispositivo tem conectividade de rede (seleção por meio do navegador da web)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

Dependendo de como você adquiriu & Inspetor de pastas de trabalho, você terá que executar dois procedimentos de desinstalação. Verifique se ambos para desinstalar completamente o software.

#### <a name="visual-studio-installer"></a>Instalador do Visual Studio

Se você tiver o Visual Studio de 2017, abra **instalador do Visual Studio**e examinar **componentes individuais** para **pastas de trabalho do Xamarin**. Se estiver marcada, desmarque-a e, em seguida, clique em **modificar** para desinstalar.

#### <a name="system-uninstall"></a>Desinstalação do sistema

Se você instalou & Inspetor de pastas de trabalho por conta própria com um instalador baixado, ela precisará ser desinstalado usando o **aplicativos e recursos** página de configurações do sistema no Windows 10 ou via **adicionar ou remover programas**no painel de controle em versões anteriores do Windows.

> **Iniciar → configurações → recursos e aplicativos de sistema →**

![](install-images/windows-remove.png "Pastas de trabalho do Xamarin e Inspetor conforme listado em &quot;aplicativos &amp; recursos&quot;")

**Você ainda deve seguir o procedimento para o instalador do Visual Studio certificar-se de pastas de trabalho & Inspetor não obter reinstalado sem seu conhecimento.**

<a name="uninstall-macos" />

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

## <a name="downgrading"></a>Fazendo downgrade

O identificador de pacote para `/Applications/Xamarin Workbooks.app` alterado de `com.xamarin.Inspector` para `com.xamarin.Workbooks` na versão 1.4 para facilitar uma divisão futura dos instaladores de pastas de trabalho do Xamarin & Inspetor.

Devido a um erro no instaladores mais antigas, não é possível fazer downgrade versões 1.4 ou mais recentes usando o 1.3.2 ou instaladores mais antigos.

Para fazer o downgrade do 1.4 ou mais recente para 1.3.2 ou anterior:

1. [Desinstalar manualmente o & Inspetor de pastas de trabalho](#macOS)
2. Execute o 1.3.2 mais antiga ou `.pkg` instalador