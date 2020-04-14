---
ms.openlocfilehash: a7c2aa15521b89e4930746dc5421ce67fa26b2b9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "67559976"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

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

    [![Captura de tela das exibições filho orientadas horizontalmente em um StackLayout, no iOS e no Android](../images/orientation.png "StackLayout que contém instâncias de rótulo orientadas horizontalmente")](../images/orientation-large.png#lightbox "StackLayout que contém instâncias de rótulo orientadas horizontalmente")

    Observe que as instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) agora estão alinhadas horizontalmente, e não verticalmente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

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

    [![Captura de tela das exibições filho orientadas horizontalmente em um StackLayout, no iOS e no Android](../images/orientation.png "StackLayout que contém instâncias de rótulo orientadas horizontalmente")](../images/orientation-large.png#lightbox "StackLayout que contém instâncias de rótulo orientadas horizontalmente")

    Observe que as instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) agora estão alinhadas horizontalmente, e não verticalmente.
