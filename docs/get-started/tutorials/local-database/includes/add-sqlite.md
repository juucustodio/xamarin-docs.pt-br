---
ms.openlocfilehash: eda3fa134d318ca787432cf5c5a2115b93c3614e
ms.sourcegitcommit: 898ba8e5140ae32a7df7e07c056aff65f6fe4260
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277720"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para concluir este tutorial, você precisará ter o Visual Studio 2019 (versão mais recente) com a carga de trabalho **Desenvolvimento mobile com .NET** instalada. Além disso, você precisará de um Mac emparelhado para compilar o aplicativo do tutorial no iOS. Para obter informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **LocalDatabaseTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **LocalDatabaseTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. Na **Gerenciador de Soluções**, selecione o projeto **LocalDatabaseTutorial**, clique com o botão direito do mouse e selecione **Gerenciar pacotes NuGet...** :

    ![Captura de tela do item de menu Gerenciar Pacotes do NuGet sendo selecionado](../images/vs/add-nuget-packages.png "Item de menu Adicionar Pacotes do NuGet")

1. No **Gerenciador de Pacotes NuGet**, selecione a guia **Procurar**, pesquise pelo pacote NuGet **sqlite-net-pcl**, selecione-o e clique no botão **Instalar** para adicioná-lo ao projeto:

    ![Captura de tela do Pacote do NuGet do SQLite.NET no Gerenciador de Pacotes do NuGet](../images/vs/add-package.png "Pacote do NuGet SQLite.NET")

    > [!NOTE]
    > Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **ID:** sqlite-net-pcl
    > - **Autor(es):** SQLite-net
    > - **Proprietário:** praeclarum
    > - **URL do Projeto:** https://github.com/praeclarum/sqlite-net
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

1. Compile a solução para garantir que não haja erros.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para concluir este tutorial, você precisará ter o Visual Studio para Mac (versão mais recente), com suporte para plataforma iOS e Android, instalado. Além disso, você também precisará do Xcode (versão mais recente). Para obter mais informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md).

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **LocalDatabaseTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **LocalDatabaseTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. Na **Painel de Soluções**, selecione o projeto **LocalDatabaseTutorial**, clique com o botão direito do mouse e selecione **Adicionar > Adicionar pacotes NuGet...** :

    ![Captura de tela do item de menu Adicionar Pacotes do NuGet sendo selecionado](../images/vsmac/add-nuget-packages.png "Item de menu Adicionar Pacotes do NuGet")

1. Na janela **Adicionar Pacotes**, pesquise pelo pacote NuGet **sqlite-net-pcl**, selecione-o e clique no botão **Adicionar Pacote** para adicioná-lo ao projeto:

    ![Captura de tela do Pacote do NuGet do SQLite.NET no Gerenciador de Pacotes do NuGet](../images/vsmac/add-package.png "Pacote do NuGet SQLite.NET")

    > [!NOTE]
    > Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Autor:** Frank A. Krueger
    > - **Id:** sqlite-net-pcl
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

1. Compile a solução para garantir que não haja erros.
