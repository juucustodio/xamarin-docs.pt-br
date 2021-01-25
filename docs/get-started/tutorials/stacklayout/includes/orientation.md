---
ms.openlocfilehash: f205e97c11c6ae1c75b101ada57c10ef49a9c146
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634733"
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

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador remoto de iOS ou do Android Emulator de sua preferência:

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

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio para Mac, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador de iOS ou do Android Emulator de sua preferência:

    [![Captura de tela das exibições filho orientadas horizontalmente em um StackLayout, no iOS e no Android](../images/orientation.png "StackLayout que contém instâncias de rótulo orientadas horizontalmente")](../images/orientation-large.png#lightbox "StackLayout que contém instâncias de rótulo orientadas horizontalmente")

    Observe que as instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) agora estão alinhadas horizontalmente, e não verticalmente.
