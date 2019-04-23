---
ms.openlocfilehash: a7b23e47269ecca4d36a344ce3589443a4a85e31
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382475"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **StackLayoutTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **StackLayoutTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **StackLayoutTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em três instâncias [`Label`](xref:Xamarin.Forms.Label) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). O [`StackLayout`](xref:Xamarin.Forms.StackLayout) posiciona suas exibições filho (as instâncias `Label`) em uma única linha, que é orientada verticalmente por padrão. Além disso, a propriedade [`Margin`](xref:Xamarin.Forms.View.Margin) indica a posição da renderização de `StackLayout` dentro de [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Além da propriedade [`Margin`](xref:Xamarin.Forms.View.Margin), as propriedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) também podem ser definidas em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). O valor da propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica a distância entre as exibições no `StackLayout`, e o valor da propriedade [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) especifica a quantidade de espaço entre cada elemento filho no `StackLayout`. Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de exibições filho em um StackLayout em iOS e Android](../images/create-stacklayout.png "StackLayout contendo instâncias de Rótulo")](../images/create-stacklayout-large.png#lightbox "StackLayout contendo instâncias de Rótulo")

    Para obter mais informações sobre [`StackLayout`](xref:Xamarin.Forms.StackLayout), confira [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **StackLayoutTutorial**. Verifique se o aplicativo usa o .NET Standard como o mecanismo de código compartilhado.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **StackLayoutTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **StackLayoutTutorial**, clique duas vezes em **MainPage.xaml** para abri-lo. Em seguida, em **MainPage.xaml**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em três instâncias [`Label`](xref:Xamarin.Forms.Label) em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). O [`StackLayout`](xref:Xamarin.Forms.StackLayout) posiciona suas exibições filho (as instâncias `Label`) em uma única linha, que é orientada verticalmente por padrão. Além disso, a propriedade [`Margin`](xref:Xamarin.Forms.View.Margin) indica a posição da renderização de `StackLayout` dentro de [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Além da propriedade [`Margin`](xref:Xamarin.Forms.View.Margin), as propriedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) também podem ser definidas em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). O valor da propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) especifica a distância entre as exibições no `StackLayout`, e o valor da propriedade [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) especifica a quantidade de espaço entre cada elemento filho no `StackLayout`. Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de exibições filho em um StackLayout em iOS e Android](../images/create-stacklayout.png "StackLayout contendo instâncias de Rótulo")](../images/create-stacklayout-large.png#lightbox "StackLayout contendo instâncias de Rótulo")

    Para obter mais informações sobre [`StackLayout`](xref:Xamarin.Forms.StackLayout), confira [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).
