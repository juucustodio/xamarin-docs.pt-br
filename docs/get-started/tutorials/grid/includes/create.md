---
ms.openlocfilehash: 28e10d1a2404d70d5329936e9bfeeea032a6fe99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61375331"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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
    > Além da propriedade [`Margin`](xref:Xamarin.Forms.View.Margin), a propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) também pode ser definida em um [`Grid`](xref:Xamarin.Forms.Grid). O valor da propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica a distância entre as exibições na `Grid`. Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS remoto ou Android Emulator escolhido:

    [![Captura de tela de um Rótulo em uma Grade, em iOS e Android](../images/create-grid.png "Grade contendo um Rótulo")](../images/create-grid-large.png#lightbox "Grade contendo um Rótulos")

    Para obter mais informações sobre [`Grid`](xref:Xamarin.Forms.Grid), confira [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

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

    [![Captura de tela de um Rótulo em uma Grade, em iOS e Android](../images/create-grid.png "Grade contendo um Rótulo")](../images/create-grid-large.png#lightbox "Grade contendo um Rótulos")

    Para obter mais informações sobre [`Grid`](xref:Xamarin.Forms.Grid), confira [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
