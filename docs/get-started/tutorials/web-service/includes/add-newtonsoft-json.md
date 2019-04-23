---
ms.openlocfilehash: aee7c7eb494af76bb450038453c19ee1ed83f2d3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388764"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **WebServiceTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **WebServiceTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. Na **Gerenciador de Soluções**, selecione o projeto **WebServiceTutorial**, clique com o botão direito do mouse e selecione **Gerenciar Pacotes do NuGet...**:

    ![Captura de tela do item de menu Adicionar pacotes NuGet sendo selecionado](../images/vs/add-nuget-packages.png "Item de menu Adicionar pacotes NuGet")

1. No **Gerenciador de Pacotes NuGet**, selecione a guia **Procurar**, pesquise pelo pacote do NuGet **Newtonsoft.Json**, selecione-o e clique no botão **Instalar** para adicioná-lo ao projeto:

    ![Captura de tela do pacote do NuGet Newtonsoft.Json no Gerenciador de Pacotes do NuGet](../images/vs/add-package.png "Pacote do NuGet Newtonsoft.Json")

    Este pacote será usado para incorporar a desserialização JSON no aplicativo.

1. Compile a solução para garantir que não haja erros.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **WebServiceTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **WebServiceTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. Na **Painel de Soluções**, selecione o projeto **WebServiceTutorial**, clique com o botão direito do mouse e selecione **Adicionar > Adicionar Pacotes do NuGet...**:

    ![Captura de tela do item de menu Adicionar pacotes NuGet sendo selecionado](../images/vsmac/add-nuget-packages.png "Item de menu Adicionar pacotes NuGet")

1. Na janela **Adicionar Pacotes**, pesquise pelo pacote do NuGet **Newtonsoft.Json**, selecione-o e clique no botão **Adicionar Pacote** para adicioná-lo ao projeto:

    ![Captura de tela do pacote do NuGet Newtonsoft.Json no Gerenciador de Pacotes do NuGet](../images/vsmac/add-package.png "Pacote do NuGet Newtonsoft.Json")

    Este pacote será usado para incorporar a desserialização JSON no aplicativo.

1. Compile a solução para garantir que não haja erros.
