---
ms.openlocfilehash: b1a041f1a2baae9b06de023f6eae9c6598b80061
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "72678703"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para concluir este tutorial, você precisará ter o Visual Studio 2019 (versão mais recente) com a carga de trabalho **Desenvolvimento mobile com .NET** instalada. Além disso, você precisará de um Mac emparelhado para compilar o aplicativo do tutorial no iOS. Para obter informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **GridTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **GridTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **GridTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Label`](xref:Xamarin.Forms.Label) em um [`Grid`](xref:Xamarin.Forms.Grid). Por padrão, o `Grid` posiciona suas exibições filho em uma única localização. Portanto, uma `Grid` que contém vários filhos devem especificar colunas e linhas, que serão abordadas no próximo exercício. Além disso, a propriedade [`Margin`](xref:Xamarin.Forms.View.Margin) indica a posição da renderização de `Grid` dentro de [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Além da propriedade [`Margin`](xref:Xamarin.Forms.View.Margin), a propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) também pode ser definida em um [`Grid`](xref:Xamarin.Forms.Grid). O valor da propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica a distância entre os limites do `Grid` e dos respectivos filhos. Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS remoto ou Android Emulator escolhido:

    [![Captura de tela de um rótulo em uma grade, no iOS e no Android](../images/create-grid.png "Grade que contém um rótulo")](../images/create-grid-large.png#lightbox "Grade que contém rótulos")

    Para obter mais informações sobre [`Grid`](xref:Xamarin.Forms.Grid), confira [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para concluir este tutorial, você precisará ter o Visual Studio para Mac (versão mais recente), com suporte para plataforma iOS e Android, instalado. Além disso, você também precisará do Xcode (versão mais recente). Para obter mais informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md).

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **GridTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **GridTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **GridTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que é composta por um [`Label`](xref:Xamarin.Forms.Label) em um [`Grid`](xref:Xamarin.Forms.Grid). Por padrão, o `Grid` posiciona suas exibições filho em uma única localização. Portanto, uma `Grid` que contém vários filhos devem especificar colunas e linhas, que serão abordadas no próximo exercício. Além disso, a propriedade [`Margin`](xref:Xamarin.Forms.View.Margin) indica a posição da renderização de `Grid` dentro de [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Além da propriedade [`Margin`](xref:Xamarin.Forms.View.Margin), a propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) também pode ser definida em um [`Grid`](xref:Xamarin.Forms.Grid). O valor da propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica a distância entre as exibições na `Grid`. Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de um rótulo em uma grade, no iOS e no Android](../images/create-grid.png "Grade que contém um rótulo")](../images/create-grid-large.png#lightbox "Grade que contém rótulos")

    Para obter mais informações sobre [`Grid`](xref:Xamarin.Forms.Grid), confira [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
