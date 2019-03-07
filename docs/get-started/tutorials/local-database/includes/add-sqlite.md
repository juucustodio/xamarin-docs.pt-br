# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **LocalDatabaseTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **LocalDatabaseTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. Na **Gerenciador de Soluções**, selecione o projeto **LocalDatabaseTutorial**, clique com o botão direito do mouse e selecione **Gerenciar pacotes NuGet...**:

    ![Captura de tela do item de menu Gerenciar pacotes NuGet sendo selecionado](../images/vs/add-nuget-packages.png "Item de menu Adicionar pacotes NuGet")

1. No **Gerenciador de Pacotes NuGet**, selecione a guia **Procurar**, pesquise pelo pacote NuGet **sqlite-net-pcl**, selecione-o e clique no botão **Instalar** para adicioná-lo ao projeto:

    ![Captura de tela do Pacote NuGet do SQLite.NET no Gerenciador de Pacotes NuGet](../images/vs/add-package.png "Pacote NuGet do SQLite.NET")

    > [!NOTE]
    > Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Autor(es):** Frank A. Krueger
    > - **Id:** sqlite-net-pcl
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

1. Compile a solução para garantir que não haja erros.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **LocalDatabaseTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **LocalDatabaseTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.
    
    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. Na **Painel de Soluções**, selecione o projeto **LocalDatabaseTutorial**, clique com o botão direito do mouse e selecione **Adicionar > Adicionar pacotes NuGet...**:

    ![Captura de tela do item de menu Adicionar pacotes NuGet sendo selecionado](../images/vsmac/add-nuget-packages.png "Item de menu Adicionar pacotes NuGet")

1. Na janela **Adicionar Pacotes**, pesquise pelo pacote NuGet **sqlite-net-pcl**, selecione-o e clique no botão **Adicionar Pacote** para adicioná-lo ao projeto:

    ![Captura de tela do Pacote NuGet do SQLite.NET no Gerenciador de Pacotes NuGet](../images/vsmac/add-package.png "Pacote NuGet do SQLite.NET")

    > [!NOTE]
    > Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Autor:** Frank A. Krueger
    > - **Id:** sqlite-net-pcl
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

1. Compile a solução para garantir que não haja erros.
