---
title: Onde posso encontrar informações e logs da minha versão?
description: Este documento descreve onde procurar para encontrar informações e logs da versão do Xamarin. Essas informações são úteis ao diagnosticar problemas, enviar bugs ou obter suporte.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 68de58f499788d803aa0af6c68f20e2265b1d6b5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013169"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Onde posso encontrar informações e logs da minha versão?

## <a name="outline"></a>Contorno

- [Informações de versão](#version-information)
  - Informações de versão do Windows
  - Informações de versão do Mac
  - Android SDK Tools, plataforma-ferramentas, compilação-ferramentas
- [Logs do IDE e do instalador](#ide-and-installer-logs)
  - [Logs do Windows](#windows-logs)
    - Xamarin Studio
    - Xamarin para Visual Studio
    - Instalador universal do Xamarin
    - Instaladores de `.msi` individuais, logs detalhados
    - Inicialização do Visual Studio, logs detalhados
  - [Logs do Mac](#mac-logs)
    - Host de compilação
  - Visual Studio para Mac
    - Xamarin Studio
    - Instalador do Xamarin
- [Saída de compilação detalhada](#verbose-build-output-logs)
- [Logs de depuração para aplicativos Xamarin. Android e Xamarin. iOS](#debug-logs-for-xamarin-apps)
  - Logs de logcat do Android `adb`
  - logs do simulador do iOS (no Mac)
  - logs de dispositivo iOS (no Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a>informações de versão do <a id="version-information" name="version-information" />

Geralmente, é melhor enviar de volta todas as informações dos botões **copiar informações** . Caso contrário, muitas vezes precisaremos solicitar informações adicionais. Por exemplo, as versões do sistema operacional, a versão do Xcode, os níveis de API do Android instalados e a versão do .NET podem ser importantes ao ajudar a solucionar um problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />informações de versão do Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Ajuda > sobre > Mostrar detalhes > copiar informações [botão]**

#### <a name="visual-studio"></a>Visual Studio

**Ajuda > sobre Microsoft Visual Studio > copiar informações [botão]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a>informações de versão do <a id="mac-version-information" name="mac-version-information" />Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Visual Studio > sobre o Visual Studio > Mostrar detalhes > copiar informações [botão]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools, plataforma-ferramentas, compilação-ferramentas

Abra o Gerenciador de SDK do Android e faça uma captura de tela da seção de **ferramentas** principais.

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Ferramentas > Abrir Gerenciador de SDK do Android**

#### <a name="visual-studio"></a>Visual Studio

**Ferramentas > Android > Abrir Gerenciador de SDK do Android...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />os logs do IDE e do instalador

Para cada local de log, não se esqueça de compactar e anexar a pasta de log inteira.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />logs do Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Ferramentas do Visual Studio para o Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> o Visual Studio 2017

[Como obter os logs de instalação do Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> o Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a>instalador "Universal" do <a id="windows-universal-installer" name="windows-universal-installer" /> Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Esses são os logs do instalador do `XamarinInstaller.exe`.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />instaladores de `.msi` individuais, logs detalhados

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Referência: [Opções de linha de comando](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />inicialização do Visual Studio, logs detalhados

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Referência: [/log (devenv. exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />logs do Mac

Você pode selecionar o item de menu **ir > ir para a pasta** no localizador e, em seguida, copiar e colar qualquer um desses caminhos na caixa de diálogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio para Mac

`~/Library/Logs/VisualStudio/7.0` (esse número pode mudar dependendo da versão que você está usando)

Essa pasta também pode ser aberta por meio de "Ajuda-> abrir diretório de log".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (esse número pode mudar dependendo da versão que você está usando)

Essa pasta também pode ser aberta por meio de "Ajuda-> abrir diretório de log".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a>instalador "Universal" do <a id="mac-universal-installer" name="mac-universal-installer" />Xamarin

`~/Library/Logs/XamarinInstaller/Universal`

Esses são os logs do instalador do `XamarinInstaller.dmg`.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a>Host de Build do <a id="mac-build-host" name="mac-build-host" />Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />saída de compilação detalhada

1. Habilite a [saída do MSBuild de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2. Para aplicativos iOS, também habilite a **saída mTouch detalhada** adicionando `-v -v -v -v` em **propriedades do projeto > Build do IOS > geral (guia) > opções adicionais > argumentos mTouch adicionais**.

3. Limpar e recompilar o projeto.

4. Copie e cole a saída da compilação do IDE em um arquivo de texto.
     - Visual Studio (Windows): **exibir > saída > Mostrar saída de: Build**
     - Visual Studio para Mac: **exibir > Pads > erros > saída da compilação (guia)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />logs de depuração para aplicativos Xamarin. Android e Xamarin. iOS

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Exibir > pads > saída do aplicativo**

(Observe que esse item de menu aparecerá somente depois que o aplicativo tiver sido iniciado.)

### <a name="visual-studio"></a>Visual Studio

**Exibir > saída > Mostrar saída de: Depurar**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [`adb`](https://developer.android.com/tools/help/adb.html) logs do logcat

Depois de executar o comando `adb`, anexe novamente o arquivo **android_logcat. txt** da área de trabalho. Essas instruções pressupõem que você tenha apenas um dispositivo anexado.

Consulte também a página [log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) .

#### <a name="visual-studio"></a>Visual Studio

1. **Ferramentas > Android > Iniciar Prompt de comando do ADB do Android**
2. Limpar o log: `adb logcat -c`
3. Reproduza o problema.
4. Saída do log: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. **Ferramentas > abrir SDK do Android prompt de comando**
2. Limpar o log: `adb logcat -c`
3. Reproduza o problema.
4. Saída do log: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />logs do simulador do iOS (no Mac)

- Para acessar o log do sistema, selecione **depurar > abrir log do sistema...** no aplicativo do simulador de Ios.

- Para exibir relatórios de falhas do simulador, abra o console. app e navegue até `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />logs de dispositivo iOS (no Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Exibir painéis de > > log do dispositivo iOS**

#### <a name="xcode"></a>Xcode

**Dispositivos > do Windows > $ {DeviceName}**

Os relatórios de falha estão disponíveis no botão **Exibir logs do dispositivo** . O log do sistema para o dispositivo aparece na parte inferior da janela sob a seta de divulgação.

#### <a name="xcode-5"></a>Xcode 5

**Windows > Gallery > dispositivos (guia) > $ {DeviceName}**
