---
ms.openlocfilehash: 1d2bed830af97ce1ff329a5396a415247a43189d
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "61372892"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Observe que a aparência de [`Button`](xref:Xamarin.Forms.Button) mudou:

    [![Captura de tela de um botão com uma aparência visual alterada, no iOS e no Android](../images/change-button-appearance.png "Botão com aparência alterada")](../images/change-button-appearance-large.png#lightbox "Botão com aparência alterada")

    Para obter mais informações sobre a configuração da aparência [`Button`](xref:Xamarin.Forms.Button), confira [Aparência do botão](~/xamarin-forms/user-interface/button.md#button-appearance) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

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

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Observe que a aparência de [`Button`](xref:Xamarin.Forms.Button) mudou:

    [![Captura de tela de um botão com uma aparência visual alterada, no iOS e no Android](../images/change-button-appearance.png "Botão com aparência alterada")](../images/change-button-appearance-large.png#lightbox "Botão com aparência alterada")

    Para obter mais informações sobre a configuração da aparência [`Button`](xref:Xamarin.Forms.Button), confira [Aparência do botão](~/xamarin-forms/user-interface/button.md#button-appearance) no guia [Botão do Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
