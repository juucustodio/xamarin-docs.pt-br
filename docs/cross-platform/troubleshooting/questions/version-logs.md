---
title: Onde posso encontrar meu logs e informações de versão?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: cdbe480c45e9c0117f1437b1ee632f6ea8f142e0
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Onde posso encontrar meu logs e informações de versão?

## <a name="outline"></a>Contorno

- [Informações de versão](#version-information)
    - Informações de versão do Windows
    - Informações de versão do Mac
    - Ferramentas de compilação de ferramentas, ferramentas de plataforma, de SDK do Android
- [Logs de IDE e instalador](#ide-and-installer-logs)
    - [Logs do Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin para Visual Studio
        - Instalador Xamarin Universal
        - Individuais `.msi` instaladores, logs detalhados
        - Inicialização do Visual Studio, logs detalhados
    - [Logs do Mac](#mac-logs)
        - Criar host
    - Visual Studio para Mac
        - Xamarin Studio
        - Instalador do Xamarin
- [Saída de compilação detalhado](#verbose-build-output-logs)
- [Logs de aplicativos xamarin e xamarin de depuração](#debug-logs-for-xamarin-apps)
    - Android `adb` logs de logcat
    - o simulador de iOS logon (Mac)
    - o dispositivo iOS logon (Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Informações de versão

Normalmente, é melhor para enviar de volta todas as informações da **copiar informações** botões. Caso contrário, é geralmente será necessário solicitar informações adicionais. Por exemplo, versões de sistema operacional, versão do Xcode instalado níveis de API do Android e versão do .NET pode estar importante ao ajudar a solucionar um problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informações de versão do Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Ajuda > sobre > Mostrar detalhes > Copiar informações [botão]**

#### <a name="visual-studio"></a>Visual Studio

**Ajuda > sobre o Microsoft Visual Studio > Copiar informações [botão]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informações de versão do Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**O Visual Studio > sobre o Visual Studio > Mostrar detalhes > Copiar informações [botão]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Ferramentas de compilação de ferramentas, ferramentas de plataforma, de SDK do Android

Abra o Gerenciador de SDK do Android e tirar uma captura de tela da parte superior **ferramentas** seção.

![](https://kb.xamarin.com/customer/portal/attachments/337323 "Captura de tela do Gerenciador de SDK do Android > pasta Ferramentas")

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Ferramentas > Abrir Gerenciador de SDK do Android**

#### <a name="visual-studio"></a>Visual Studio

Selecione o ícone de barra de ferramentas do Gerenciador de SDK:

![](https://kb.xamarin.com/customer/portal/attachments/420238 "Iniciar o ícone de barra de ferramentas do Gerenciador de SDK do Android no Visual Studio")

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Logs de IDE e instalador

Para cada local de log, certifique-se de anexar a pasta de log inteiro e compacte.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Logs do Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Ferramentas do Visual Studio para Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> 2017 do Visual Studio

[Como obter os logs de instalação do Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Instalador de "Universal" Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Estes são os logs a partir de `XamarinInstaller.exe` installer.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Individuais `.msi` instaladores, logs detalhados

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Referência: [opções de linha de comando](http://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Inicialização do Visual Studio, logs detalhados

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Referência: [/Log (devenv.exe)](http://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Logs do Mac

Você pode selecionar o **Ir > vá para a pasta** menu item localizador e, em seguida, copiar e colar qualquer um desses caminhos na caixa de diálogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio para Mac

`~/Library/Logs/VisualStudio/7.0` (esse número pode mudar dependendo da versão que você está usando)

Essa pasta também pode ser aberta por meio de "Help -> abrir diretório de Log".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (esse número pode mudar dependendo da versão que você está usando)

Essa pasta também pode ser aberta por meio de "Help -> abrir diretório de Log".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Instalador de "Universal" Xamarin

`~/Library/Logs/XamarinInstaller/Universal`

Estes são os logs a partir de `XamarinInstaller.dmg` installer.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host de Build Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Saída de compilação detalhado

1.  Habilitar [saída de diagnóstico MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Para aplicativos do iOS, também habilitar **saída detalhada mtouch** adicionando `-v -v -v -v` em **propriedades do projeto > iOS compilação > Geral [tab] > Opções adicionais > argumentos adicionais mtouch**.

    **O Visual Studio (Windows)**

    [![](https://kb.xamarin.com/customer/portal/attachments/337319 "Insira o traço quatro v para o campo de entrada de argumentos adicionais mtouch")](https://kb.xamarin.com/customer/portal/attachments/337319)

    **Visual Studio para Mac**

    [![](https://kb.xamarin.com/customer/portal/attachments/337316 "Insira o traço quatro v para o campo de entrada de argumentos adicionais mtouch")](https://kb.xamarin.com/customer/portal/attachments/337316)

3.  Limpe e recompile o projeto.

4.  Copie e cole a saída da compilação do IDE em um arquivo de texto.
     - O Visual Studio (Windows): **exibição > saída > Mostrar saída de: compilação**
     - O Visual Studio para Mac: **exibição > preenche > erros > [tab] de saída de compilação**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Logs de aplicativos xamarin e xamarin de depuração

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Modo de exibição > preenche > saída do aplicativo**

![](https://kb.xamarin.com/customer/portal/attachments/337290 "Ícone de painel de saída do aplicativo no Visual Studio para Mac")


(Observe que este item de menu só será exibida depois que o aplicativo foi iniciado.)

### <a name="visual-studio"></a>Visual Studio

**Modo de exibição > saída > Mostrar saída de: depurar**

![](https://kb.xamarin.com/customer/portal/attachments/337292 "Painel de saída mostrando a opção de depuração no Visual Studio")

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](http://developer.android.com/tools/help/adb.html) logcat logs

Depois de executar o `adb` command, anexar novamente o **android_logcat.txt** arquivo da área de trabalho. Essas instruções presumem que você tem apenas um dispositivo conectado.

Consulte também o [o Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) página.

#### <a name="visual-studio"></a>Visual Studio

1.  **Ferramentas > Android > Inicie o Prompt de comando Adb Android**
2.  Limpe o log: `adb logcat -c`
3.  Reproduza o problema.
4.  O log de saída: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1.  **Ferramentas > Abra o Prompt de comando do SDK do Android**
2.  Limpe o log: `adb logcat -c`
3.  Reproduza o problema.
4.  O log de saída: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />o simulador de iOS logon (Mac)

*   Para acessar o log do sistema, selecione **Depurar > Abrir Log do sistema...**  no aplicativo do simulador de iOS.

    ![](https://kb.xamarin.com/customer/portal/attachments/382617 "Mostrando a opção de abrir o Log de sistema do menu Depurar")

*   Para exibir relatórios de falha a partir do simulador, abra Console.app e navegue até `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />o dispositivo iOS logon (Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Modo de exibição > preenche > iOS registro de dispositivo**

#### <a name="xcode"></a>Xcode 

**Janela > dispositivos > ${DeviceName}**

Relatórios de falha estão disponíveis sob o **Exibir Logs de dispositivo** botão. O log do sistema para o dispositivo aparece na parte inferior da janela em direção a divulgação de informações <img alt="Disclosure arrow" src="https://kb.xamarin.com/customer/portal/attachments/382618" style="width: 15px; height: 12px;" />.

#### <a name="xcode-5"></a>Xcode 5

**Janela > organizador > dispositivos [tab] > ${DeviceName}**
