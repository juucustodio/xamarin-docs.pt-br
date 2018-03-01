---
title: "Como ressincronizar manualmente o Xamarin licenças?"
ms.topic: article
ms.prod: xamarin
ms.assetid: D0BD93E9-3A1F-4E5B-8EE8-36ADC33DCFE4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 2413b6b7563a6ed1e17a8db61d2d61ddc85e71ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-manually-resynchronize-xamarin-licenses"></a>Como ressincronizar manualmente o Xamarin licenças?

> [!IMPORTANT]
> Este guia não se aplicam à maioria dos usuários do MSDN porque eles não são necessários para o proprietário ou de log em contas do Xamarin usando o [Xamarin componentes armazenar](https://components.xamarin.com/) ou [Visual Studio para Mac (Mac)](~/cross-platform/get-started/requirements.md).




## <a name="overview"></a>Visão geral

Geralmente as informações de licença serão ser sincronizadas com o servidor de licença do Xamarin automaticamente quando você inicia o IDE. Por exemplo, atualizações de licença, downgrades e renovações normalmente aparecerão automaticamente depois de reiniciar o IDE. As informações de licença mostradas no IDE devem corresponder as informações mostradas no seu [página conta](https://store.xamarin.com/account/my/subscription/computers). Se as informações ainda não correspondentes, você pode, primeiro, verifique se as informações da conta de armazenamento parece corretas e ressincronizar manualmente as licenças de logoff, excluindo os arquivos de licença no disco e, em seguida, fazer logon novamente.

### <a name="note-for-ios-developers-on-windows"></a>Observação para os desenvolvedores de iOS no Windows

os desenvolvedores de iOS no Windows talvez seja necessário também executar essas etapas para o Visual Studio para Mac; mesmo se você não desenvolver diretamente em seu host de compilação de Mac emparelhado.

## <a name="quick-manual-refresh-steps"></a>Etapas de atualização manual rápida

Esse processo rápido ignora a etapa de excluir os arquivos de licença em disco que pode ser suficiente em alguns casos. 

1.  Fazer logoff de sua conta Xamarin por meio do IDE:
    -   Visual Studio para Mac
        -   Canto superior direito da tela de boas-vinda
        -   **O Visual Studio para Mac > conta** (Mac)
        -   **Ferramentas > conta** (Windows)
    -   Visual Studio
        -   **Ferramentas > conta Xamarin**
2.  Faça logon novamente na conta Xamarin no IDE.

## <a name="extended-manual-license-refresh-steps"></a>Estendido etapas de atualização manual de licença

1.  Faça logoff de sua conta Xamarin por meio do IDE. 
2.  Feche o IDE.
3.  Verifique se as informações da conta de armazenamento parecem corretas. Verificar nomes de computador duplicado em particular no [página computadores](https://store.xamarin.com/account/my/subscription/computers).

4.  Se você vir um par de nomes de computador duplicado, use o **desativar** item de menu suspenso para remover _ambos_ membros do par:
    
    ![Licença -> desative no https://store.xamarin.com/account/my/subscription/computers](resync-licenses-images/deactivate.png "usar o item de menu suspenso desativar para remover os dois membros do par")

5.  Exclua cópias restantes, os arquivos de licença ainda estiver presente no disco.
    -   Windows

        Exclua todas as pastas a seguir:
        -   `%PROGRAMDATA%\Mono for Android`
        -   `%PROGRAMDATA%\MonoTouch`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\Mono for Android`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\MonoTouch`

        Nas instalações padrão do Windows:
        -   `%PROGRAMDATA%` se expandirá para `C:\ProgramData`
        -   `%LOCALAPPDATA%` se expandirá para `C:\Users\%USERNAME%\AppData\Local`

        O `VirtualStore` cópias das licenças são criadas automaticamente pelo Windows em determinados cenários. Se as cópias VirtualStore existirem, eles serão lidos _em vez disso,_ de licenças em `%PROGRAMDATA%`.

    -   Mac

        Exclua todas as pastas a seguir:

        -   `~/Library/MonoAndroid`
        -   `~/Library/MonoTouch`
        -   `~/Library/Xamarin.Mac`

        Você pode acessar esses caminhos em **localizador** selecionando o **Ir > vá para a pasta** menu e colá-los na janela de diálogo. Localizador substitui automaticamente o ~ caractere til com sua pasta de usuário.

6.  Abra o Visual Studio para Mac ou o Visual Studio e log volta para sua conta do Xamarin.

## <a name="supplementary-information-individual-license-file-locations"></a>Informações complementares: locais de arquivo de licença individual

### <a name="windows"></a>Windows

No Windows, os arquivos de licença individuais são armazenados nos seguintes locais:

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.licx`

Licenças para *avaliação* assinaturas são nomeadas de forma diferente:

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.trial.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.trial.licx`

### <a name="mac"></a>Mac

No Mac os arquivos de licença individuais são armazenados nos seguintes locais:

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.v2`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License`

Licenças para *avaliação* assinaturas são nomeadas de forma diferente:

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License.trial`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.trial`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License.trial`

## <a name="additional-troubleshooting-steps"></a>Etapas de solução de problemas adicionais

-   Verifique se você possui caracteres não ASCII em seu nome de usuário, no nome do computador ou em qualquer um dos caminhos totalmente expandidos dos arquivos de licença.

-   [Entre em contato com o suporte ao desenvolvedor do Xamarin](http://xamarin.com/support)
