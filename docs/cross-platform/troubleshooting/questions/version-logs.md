---
title: Onde posso encontrar informações e logs da minha versão?
description: Este documento descreve onde pesquisar para localizar os logs e informações de versão do Xamarin. Essas informações são úteis ao diagnosticar problemas, enviando bugs ou como obter suporte.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e389bc33538ec3c3d36eb749c746f5a4723aab3c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830363"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Onde posso encontrar informações e logs da minha versão?

## <a name="outline"></a>Contorno

- [Informações de versão](#version-information)
    - Informações de versão do Windows
    - Informações de versão do Mac
    - Ferramentas de Build do Android SDK Tools, ferramentas de plataforma
- [Logs do IDE e installer](#ide-and-installer-logs)
    - [Logs do Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin para Visual Studio
        - Instalador Universal do Xamarin
        - Individuais `.msi` instaladores, logs detalhados
        - Inicialização do Visual Studio, os logs detalhados
    - [Logs do Mac](#mac-logs)
        - Host de build
    - Visual Studio para Mac
        - Xamarin Studio
        - Instalador do Xamarin
- [Saída da compilação detalhado](#verbose-build-output-logs)
- [Depurar os logs para aplicativos xamarin. Android e xamarin. IOS](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat logs
    - simulador de iOS registra em log (no Mac)
    - logs de dispositivo iOS (no Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Informações de versão

Geralmente é melhor para enviar de volta todas as informações do **copiar informações** botões. Caso contrário, é geralmente necessário solicitar informações adicionais. Por exemplo, versões de sistema operacional, versão do Xcode instalado níveis de API do Android e versão do .NET pode todos ser importante quando ajudando a solucionar um problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informações de versão do Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Ajuda > sobre > Mostrar detalhes > Copiar informações [botão]**

#### <a name="visual-studio"></a>Visual Studio

**Ajuda > sobre o Microsoft Visual Studio > Copiar informações [botão]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informações de versão do Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Visual Studio > sobre o Visual Studio > Mostrar detalhes > Copiar informações [botão]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Ferramentas de Build do Android SDK Tools, ferramentas de plataforma

Abra o Gerenciador de SDK do Android e tirar uma captura de tela da parte superior **ferramentas** seção.

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Ferramentas > Abrir Gerenciador de SDK do Android**

#### <a name="visual-studio"></a>Visual Studio

**Ferramentas > Android > Abrir Gerenciador de SDK do Android...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Logs do IDE e installer

Para cada local de log, certifique-se compactar e anexar a pasta de log inteiro.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Logs do Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Ferramentas do Visual Studio para Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Como obter os logs de instalação do Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> "Universal" instalador do Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Esses são os logs a partir de `XamarinInstaller.exe` installer.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Individuais `.msi` instaladores, logs detalhados

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Referência: [Opções de linha de comando](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Inicialização do Visual Studio, os logs detalhados

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Reference: [/Log (devenv.exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Logs do Mac

Você pode selecionar o **vá > vá para a pasta** menu do item no Finder e, em seguida, copie e cole qualquer um desses caminhos na caixa de diálogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio para Mac

`~/Library/Logs/VisualStudio/7.0` (esse número pode mudar dependendo da versão que você está usando)

Essa pasta também pode ser aberta por meio de "Ajuda -> abrir diretório de Log".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (esse número pode mudar dependendo da versão que você está usando)

Essa pasta também pode ser aberta por meio de "Ajuda -> abrir diretório de Log".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Xamarin "Universal" installer

`~/Library/Logs/XamarinInstaller/Universal`

Esses são os logs a partir de `XamarinInstaller.dmg` installer.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host de Build do Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Saída da compilação detalhado

1.  Habilitar [resultados de diagnóstico do MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Para aplicativos iOS, também permitem **saída detalhada do mtouch** adicionando `-v -v -v -v` sob **as propriedades do projeto > Build do iOS > geral (guia) > Opções adicionais > argumentos adicionais do mtouch**.

3.  Limpe e recompile o projeto.

4.  Copie e cole a saída do build do IDE em um arquivo de texto.
     - Visual Studio (Windows): **Exibir > saída > Mostrar saída de: Build**
     - O Visual Studio para Mac: **Modo de exibição > painéis > erros > saída do Build (guia)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Depurar os logs para aplicativos xamarin. Android e xamarin. IOS

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Modo de exibição > painéis > saída do aplicativo**

(Observe que este item de menu só aparecerá depois que o aplicativo foi iniciado.)

### <a name="visual-studio"></a>Visual Studio

**Exibir > saída > Mostrar saída de: Depurar**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](https://developer.android.com/tools/help/adb.html) logcat logs

Depois de executar o `adb` de comando, anexe back o **android_logcat.txt** arquivo da área de trabalho. Essas instruções pressupõem que você tem apenas um dispositivo conectado.

Consulte também o [Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) página.

#### <a name="visual-studio"></a>Visual Studio

1. **Ferramentas > Android > Iniciar o Prompt de comando do Adb do Android**
2. Limpe o log: `adb logcat -c`
3. Reproduza o problema.
4. O log de saída: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. **Ferramentas > Abrir Prompt de comando do SDK do Android**
2. Limpe o log: `adb logcat -c`
3. Reproduza o problema.
4. O log de saída: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />simulador de iOS registra em log (no Mac)

* Para acessar o log do sistema, selecione **Depurar > Abrir Log do sistema...**  no aplicativo de simulador do iOS.

* Para exibir relatórios de pane do simulador, abra Console.app e navegue até `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />logs de dispositivo iOS (no Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Exibir > Pads > Log do dispositivo do iOS**

#### <a name="xcode"></a>Xcode

**Janela > dispositivos > ${DeviceName}**

Relatórios de falha estão disponíveis sob o **Exibir Logs de dispositivo** botão. O log do sistema para o dispositivo aparece na parte inferior da janela sob a seta de divulgação não autorizada.

#### <a name="xcode-5"></a>Xcode 5

**Janela > organizador > dispositivos (guia) > ${DeviceName}**
