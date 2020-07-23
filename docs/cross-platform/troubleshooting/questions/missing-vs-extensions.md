---
title: Extensões do Visual Studio ausentes após a instalação
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: f1e230d8851545e890c806b58ddf26647764bce9
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938808"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Extensões do Visual Studio ausentes após a instalação

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Mensagem de erro: este projeto é incompatível com a edição atual do Visual Studio

Verifique se o Visual Studio 2017 (Community, Professional ou Enterprise) ou mais recente está instalado.

Consulte também os [requisitos do Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Correção possível 1: altere a instalação para verificar se as extensões do Visual Studio estão instaladas

Em determinadas situações, o instalador do Xamarin pode desmarcar automaticamente as opções de instalação das extensões do Visual Studio. Se essa for a causa do problema, instale as extensões ausentes do Visual Studio usando o comando **Alterar** do instalador. Por exemplo, para instalar as extensões do Visual Studio 2013:

1. Abra o Painel de Controle **Programas e Recursos** do Windows.

2. Clique com botão direito do mouse na entrada **Xamarin** e selecione **Alterar**.

3. Clique em **Avançar**e, em seguida, **Alterar**.

4. Verifique se a opção **Xamarin para Visual Studio 2013** está definida como instalar:

    ![Habilitar a opção de instalação do Xamarin para Visual Studio 2013](missing-vs-extensions-images/installer.png)

5. Continue com o restante do Assistente de instalação.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Correção possível 2: solicitar que o Visual Studio configure as extensões novamente

1. Verifique se as extensões do Xamarin foram copiadas para a pasta de extensões do Visual Studio:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Se as extensões estiverem instaladas corretamente (para a versão 3.1.228), haverá 60 itens na pasta:

    ![](missing-vs-extensions-images/folder.png "List of 'Xamarin\3.1.228.0' folder contents in Explorer")

2. Depois de confirmar que essa pasta parece correta, informe ao Visual Studio para tentar configurar as extensões novamente:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Correção possível 3: Tente uma reinstalação nova do Xamarin

1. No Painel de Controle do Windows, desinstale qualquer um dos seguintes itens que estiverem presentes:

    * Xamarin

    * Xamarin para Windows

    * Xamarin.Android

    * Xamarin.iOS

    * Xamarin para Visual Studio

2. No Explorer, exclua todos os arquivos restantes das pastas de extensão do Xamarin Visual Studio (todas as versões, incluindo **arquivos de programas** e **arquivos de programas (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3. Verifique também no diretório "VirtualStore" para ver se há alguma cópia de "sobreposição" de qualquer um dos diretórios de extensão:

    `%LOCALAPPDATA%\VirtualStore`

4. Abra o Editor do Registro (regedit).

5. Procure por esta chave:

    _HKEY \_ local \_ MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6. Localize e exclua as entradas que correspondem a esse padrão:

    _C:\Arquivos de programas \* \Microsoft Visual Studio 1 \* . 0 \ Common7\IDE\Extensions\Xamarin_

7. Procure por esta chave:

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8. Exclua as entradas que parecem ser relacionadas ao Xamarin. Por exemplo, aqui está uma que costumava causar problemas em versões mais antigas do Xamarin:

    _Mono.VisualStudio.Shell,1.0_

9. Reinicialize.

10. Reinstale a versão estável atual do Xamarin em [VisualStudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Correção possível 4: reparar a instalação do Visual Studio

1. Abra o Painel de Controle **Programas e Recursos** do Windows.

2. Clique com botão direito do mouse na entrada pertinente do Microsoft Visual Studio e selecione **Alterar**

3. Clique no botão **Reparar** na caixa de diálogo do Visual Studio que é aberta.
