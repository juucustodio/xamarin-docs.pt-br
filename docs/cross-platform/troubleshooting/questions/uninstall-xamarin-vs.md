---
title: Como executar uma desinstalação completa do Xamarin para Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: e387e398f3a79ea2063457f0c5c6e7469c07ac23
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521535"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>Como executar uma desinstalação completa do Xamarin para Visual Studio?


1. No Painel de Controle do Windows, desinstale qualquer um dos seguintes itens que estiverem presentes:

    - Xamarin
    - Xamarin para Windows
    - Xamarin.Android
    - Xamarin.iOS
    - Xamarin para Visual Studio

2. No Explorer, exclua todos os arquivos restantes nas pastas de extensão do Xamarin para Visual Studio (todas as versões, incluindo _Arquivos de Programas_ e _Arquivos de Programas (x86)_ ):

    _C:\\Arquivos de Programas\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

3. Exclua também o diretório de cache do componente MEF (Managed Extensibility Framework) do Visual Studio:

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    Na verdade, essa etapa por si só geralmente é suficiente para resolver erros, como:

    - "O pacote 'XamarinShellPackage' não foi carregado corretamente"

    - "Não é possível carregar o arquivo de projeto... Há um subtipo de projeto ausente"

    - "Referência de objeto não definida para uma instância de um objeto.  em Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()"

    - "Falha de SetSite para o pacote" (no _ActivityLog.xml_ do Visual Studio)

    - "Falha de LegacySitePackage para o pacote" (no _ActivityLog.xml_ do Visual Studio)

    (Consulte também a extensão [Limpar Cache de Componente MEF](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd) do Visual Studio.  E consulte o [Bug 40781, comentário 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) para um pouco mais contexto sobre o problema upstream no Visual Studio que pode causar esses erros.)

4. Verifique também no diretório _VirtualStore_ para ver se o Windows pode ter armazenado arquivos de sobreposição para os diretórios _Extensões\\Xamarin_ ou _ComponentModelCache_ em:

    _%LOCALAPPDATA%\\VirtualStore_

5. Abra o Editor de Registro (`regedit`).

6. Procure a seguinte chave:

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7. Localize e exclua as entradas que correspondem a esse padrão:

    _C:\\Arquivos de Programas\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

8. Procure por esta chave:

    _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9. Exclua as entradas que parecem ser relacionadas ao Xamarin.  Por exemplo, aqui está uma que costumava causar problemas em versões mais antigas do Xamarin:

    _Mono.VisualStudio.Shell,1.0_

10. Abra um prompt de comando `cmd.exe` como administrador e execute os comandos `devenv /setup` e `devenv /updateconfiguration` para cada versão instalada do Visual Studio.  Por exemplo, para o Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Reinicialize.

12. Reinstale a versão estável atual do Xamarin usando do [VisualStudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Etapas adicionais de solução de problemas para "o pacote não foi carregado corretamente"

Aqui estão mais algumas etapas para experimentar, caso as etapas acima não resolvam o erro "o pacote não foi carregado corretamente".

1. Crie uma nova conta de usuário do Windows.

2. Verifique se as extensões do Xamarin para Visual Studio carregam sem erro para o novo usuário.

3. Se as extensões carregarem corretamente, o problema será provavelmente causado por algumas das configurações armazenadas do usuário original:

    - **No Explorer** – _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0_
    - **No regedit** – _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0_
    - **No regedit** – _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\_Config_

4. Se essas configurações armazenadas realmente parecem ser o problema, você pode tentar fazer o backup e, em seguida, excluí-las.
