---
ms.openlocfilehash: a7c2aa15521b89e4930746dc5421ce67fa26b2b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382479"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`StackLayout`](xref:Xamarin.Forms.StackLayout) para que seus elementos filho sejam alinhados horizontalmente, e não verticalmente:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Este código define a propriedade [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) como [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de exibições filho orientadas horizontalmente, no iOS e Android](../images/orientation.png "StackLayout contendo instâncias de Rótulo orientadas horizontalmente")](../images/orientation-large.png#lightbox "StackLayout contendo instâncias de Rótulo orientadas horizontalmente")

    Observe que as instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) agora estão alinhadas horizontalmente, e não verticalmente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`StackLayout`](xref:Xamarin.Forms.StackLayout) para que seus elementos filho sejam alinhados horizontalmente, e não verticalmente:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Este código define a propriedade [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) como [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de exibições filho orientadas horizontalmente, no iOS e Android](../images/orientation.png "StackLayout contendo instâncias de Rótulo orientadas horizontalmente")](../images/orientation-large.png#lightbox "StackLayout contendo instâncias de Rótulo orientadas horizontalmente")

    Observe que as instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) agora estão alinhadas horizontalmente, e não verticalmente.
