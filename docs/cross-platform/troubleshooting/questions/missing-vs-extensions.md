---
title: Extensões do Visual Studio ausentes após a instalação
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: 72870b9bf6ff6c3068ee037e6405e4ec03546cd6
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Extensões do Visual Studio ausentes após a instalação

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Mensagem de erro: Este projeto é incompatível com a edição atual do Visual Studio

Verifique se uma versão compatível do Visual Studio está instalada:

-   De 2017 Visual Studio (Community, Professional ou Enterprise)
-   Visual Studio 2015 (Community, Professional ou Enterprise)

Consulte também o [requisitos do Windows](~/cross-platform/get-started/requirements.md#windows).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Corrigir possíveis 1: Alterar a instalação para verificar se que as extensões do Visual Studio estão instaladas

Em determinadas situações, o instalador do Xamarin pode desmarcar automaticamente as opções de instalação das extensões do Visual Studio. Se essa for a causa do problema, instale as extensões ausentes do Visual Studio usando o comando **Alterar** do instalador. Por exemplo, para instalar as extensões do Visual Studio 2013:

1. Abra o Painel de Controle **Programas e Recursos** do Windows.

2. Clique com botão direito do mouse na entrada **Xamarin** e selecione **Alterar**.

3. Clique em **Avançar**e, em seguida, **Alterar**.

4. Verifique se o **Xamarin para Visual Studio 2013** opção é definida para instalar:

    ![](missing-vs-extensions-images/installer.png "Habilitar o Xamarin para a opção de instalação do Visual Studio 2013")

5. Continue com o restante do Assistente de instalação.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Correção possível 2: solicitar que o Visual Studio configure as extensões novamente

1. Verifique se as extensões do Xamarin foram copiadas para a pasta de extensões do Visual Studio:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Se as extensões são instaladas corretamente (para a versão 3.1.228), haverá 60 itens na pasta:


    ![](missing-vs-extensions-images/folder.png "Lista de conteúdo da pasta 'Xamarin\3.1.228.0' no Explorer")

2. Depois de confirmar que essa pasta parece correta, informe ao Visual Studio para tentar configurar as extensões novamente:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Correção possível 3: Tente uma reinstalação nova do Xamarin

1.  No Painel de Controle do Windows, desinstale qualquer um dos seguintes itens que estiverem presentes:

    *   Xamarin

    *   Xamarin para Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin para Visual Studio

2.  No Explorer, exclua todos os arquivos restantes nas pastas de extensão do Xamarin para Visual Studio (todas as versões, incluindo **Arquivos de Programas** e **Arquivos de Programas (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  Verifique também no diretório "VirtualStore" para ver se há alguma cópia de "sobreposição" de qualquer um dos diretórios de extensão:

    `%LOCALAPPDATA%\VirtualStore`

4.  Abra o Editor do Registro (regedit).

5.  Procure por esta chave:

    _HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  Localize e exclua as entradas que correspondem a esse padrão:

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  Procure por esta chave:

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  Exclua as entradas que parecem ser relacionadas ao Xamarin. Por exemplo, aqui está uma que costumava causar problemas em versões mais antigas do Xamarin:

    _Mono.VisualStudio.Shell,1.0_

9.  Reinicialize.

10.  Reinstale a versão estável atual do Xamarin do [visualstudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Correção possível 4: reparar a instalação do Visual Studio

1.  Abra o Painel de Controle **Programas e Recursos** do Windows.

2.  Clique com botão direito do mouse na entrada pertinente do Microsoft Visual Studio e selecione **Alterar**

3.  Clique no botão **Reparar** na caixa de diálogo do Visual Studio que é aberta.
