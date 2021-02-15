---
ms.openlocfilehash: 703d99446d3a43411b681677bf9d3c9ab01ea83f
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690033"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Button`](xref:Xamarin.Forms.Button) para alterar sua aparência visual:

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Esse código define propriedades que alteram a aparência visual do [`Button`](xref:Xamarin.Forms.Button). A propriedade [`TextColor`](xref:Xamarin.Forms.Button.TextColor) define a cor do texto `Button` e a propriedade [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) define a cor do segundo plano para o texto. A propriedade [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) define a cor de uma área ao redor do `Button` e a propriedade [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) define a largura da borda. Por padrão, o `Button` é retangular, mas pode receber cantos arredondados configurando a propriedade [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) para um valor apropriado. Além disso, o tamanho do `Button` é alterado configurando sua propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador remoto de iOS ou do Android Emulator de sua preferência. Observe que a aparência de [`Button`](xref:Xamarin.Forms.Button) mudou:

    [![Captura de tela de um botão com uma aparência visual alterada, no iOS e no Android](../images/change-button-appearance.png "Botão com aparência alterada")](../images/change-button-appearance-large.png#lightbox "Botão com aparência alterada")

    Interrompa o aplicativo no Visual Studio.

    Para obter mais informações sobre a configuração da aparência [`Button`](xref:Xamarin.Forms.Button), confira [Aparência do botão](~/xamarin-forms/user-interface/button.md#button-appearance) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Button`](xref:Xamarin.Forms.Button) para alterar sua aparência visual:

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Esse código define propriedades que alteram a aparência visual do [`Button`](xref:Xamarin.Forms.Button). A propriedade [`TextColor`](xref:Xamarin.Forms.Button.TextColor) define a cor do texto `Button` e a propriedade [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) define a cor do segundo plano para o texto. A propriedade [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) define a cor de uma área ao redor do `Button` e a propriedade [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) define a largura da borda. Por padrão, o `Button` é retangular, mas pode receber cantos arredondados configurando a propriedade [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) para um valor apropriado. Além disso, o tamanho do `Button` é alterado configurando sua propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio para Mac, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador de iOS ou do Android Emulator de sua preferência. Observe que a aparência de [`Button`](xref:Xamarin.Forms.Button) mudou:

    [![Captura de tela de um botão com uma aparência visual alterada, no iOS e no Android](../images/change-button-appearance.png "Botão com aparência alterada")](../images/change-button-appearance-large.png#lightbox "Botão com aparência alterada")

    Interrompa o aplicativo no Visual Studio para Mac.

    Para obter mais informações sobre a configuração da aparência [`Button`](xref:Xamarin.Forms.Button), confira [Aparência do botão](~/xamarin-forms/user-interface/button.md#button-appearance) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
