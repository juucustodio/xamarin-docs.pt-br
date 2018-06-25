---
title: Solução de problemas de configuração do emulador
description: Este artigo explica como diagnosticar e solucionar problemas que podem ocorrer ao usar o Android Device Manager.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: 4cbd7d7626d114856d6c68fe7bc9feb7c2a5abc2
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733242"
---
# <a name="troubleshooting-emulator-setup-problems"></a>Solução de problemas de configuração do emulador

_Este artigo explica como diagnosticar e solucionar problemas que podem ocorrer ao usar o Android Device Manager para configurar o Android Emulator. Para obter informações sobre como solucionar problemas ao executar o Android Emulator, consulte [Solução de problemas do Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md)._

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="android-sdk-in-non-standard-location"></a>SDK do Android em local não padrão

Normalmente, o SDK do Android está instalado neste local:

**C:\\Program Files (x86)\\Android\\android-sdk**

Se o SDK não estiver instalado nesse local, você poderá ver este erro ao iniciar o Android Device Manager:

![Erro de instância do SDK do Android](troubleshooting-images/win/01-sdk-error.png)

Para solucionar esse problema, faça o seguinte:

1. Na área de trabalho do Windows, navegue até **C:\\Users\\*nomedeusuário*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Local do arquivo de log do Android Device Manager](troubleshooting-images/win/02-log-files.png)

2. Clique duas vezes para abrir um dos arquivos de log e localize o **Caminho do arquivo de configuração**. Por exemplo:

    [![Caminho do arquivo de configuração no arquivo de log](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. Navegue até esse local e clique duas vezes em **user.config** para abri-lo. 

4. Em **user.config**, localize o elemento **&lt;UserSettings&gt;** e adicione um atributo **AndroidSdkPath** a ele. Defina esse atributo no caminho em que o SDK do Android está instalado no computador e salve o arquivo. Por exemplo, **&lt;UserSettings&gt;** seria parecido com o seguinte se o SDK do Android estivesse instalado em **C:\\Programs\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Depois de fazer essa alteração em **user.config**, você poderá iniciar o Android Device Manager.

## <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Android Device Manager.

2. No menu de opções adicionais, clique em **Revelar no Explorer**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

    ![Local do arquivo snapshot.pb](troubleshooting-images/win/05-delete-snapshot.png)

5. Reinicie o AVD. 

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="snapshot-disables-wifi-on-android-oreo"></a>O Snapshot desabilita o WiFi no Android Oreo

Se você configurou um AVD para o Android Oreo com acesso Wi-Fi simulado, reiniciar o AVD após um instantâneo pode fazer com esse acesso seja desabilitado.

Para solucionar esse problema:

1. Selecione o AVD no Android Device Manager.

2. No menu de opções adicionais, clique em **Revelar no Localizador**.

3. Navegue até **snapshots > default_boot**.

4. Exclua o arquivo **snapshot.pb**:

    [![Local do arquivo snapshot.pb](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. Reinicie o AVD. 

Depois que essas alterações forem feitas, o AVD será reiniciado em um estado que permita que o Wi-Fi funcione novamente.


-----

## <a name="generating-a-bug-report"></a>Gerando um relatório de bugs

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se encontrar um problema com o Android Device Manager que não pode ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando com o botão direito do mouse na barra de título e selecionando **Gerar Relatório de Bugs**:

[![Local do item de menu para enviar um relatório de bugs](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Se encontrar um problema com o Android Device Manager que não possa ser resolvido usando as dicas de solução de problemas acima, envie um relatório de bugs clicando em **Ajuda > Gerar Relatório de Bugs**:

[![Local do item de menu para enviar um relatório de bugs](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)

-----
