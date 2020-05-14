---
ms.openlocfilehash: 97e52d593e2b59d127da324d8ad74ad7b2096cdd
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343413"
---
O tamanho e a posição das exibições filho dentro de um [`StackLayout`](xref:Xamarin.Forms.StackLayout) dependem dos valores das propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) da exibição filho e dos valores das propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

As propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) podem ser definidas como campos do struct [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions), que encapsula duas preferências de layout:

- **Alinhamento** – o alinhamento preferencial da exibição filho, que determina sua posição e tamanho dentro do layout pai.
- **Expansão** – indica se a exibição filho deve usar espaço extra se estiver disponível (usado apenas por um [`StackLayout`](xref:Xamarin.Forms.StackLayout)).

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`StackLayout`](xref:Xamarin.Forms.StackLayout) para definir opções de alinhamento e expansão para cada [`Label`](xref:Xamarin.Forms.Label):

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Esse código define preferências de alinhamento nas primeiras quatro instâncias de [`Label`](xref:Xamarin.Forms.Label), bem como preferências de expansão nas quatro últimas instâncias de `Label`. Os campos [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) e [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) são usados para definir o alinhamento do das instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) pai. Os campos [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) e [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) são usados para definir a preferência de alinhamento e definir se a exibição ocupará mais espaço, se disponível, dentro do `StackLayout` pai.

    > [!NOTE]
    > O valor padrão das propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de uma exibição é [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de exibições filho em um StackLayout, com opções de alinhamento e expansão definidas, no iOS e no Android](../images/alignment-expansion.png "StackLayout contendo instâncias de rótulo, com alinhamento e expansão definidos")](../images/alignment-expansion-large.png#lightbox "StackLayout contendo instâncias de rótulo, com alinhamento e expansão definidos")

    Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) respeita somente as preferências de alinhamento em exibições filho que estão na direção oposta à orientação `StackLayout`. Portanto, as exibições filho de [`Label`](xref:Xamarin.Forms.Label) dentro do `StackLayout` orientado verticalmente definem suas propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) como um dos campos de alinhamento:

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), que posiciona o [`Label`](xref:Xamarin.Forms.Label) à esquerda do [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), que centraliza o [`Label`](xref:Xamarin.Forms.Label) no [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), que posiciona o [`Label`](xref:Xamarin.Forms.Label) à direita do [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), que garante que o [`Label`](xref:Xamarin.Forms.Label) preencha a largura do [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) só pode expandir exibições filho na direção de sua orientação. Portanto, o `StackLayout` orientado verticalmente pode expandir exibições filho de [`Label`](xref:Xamarin.Forms.Label) que definem suas propriedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como um dos campos de expansão. Isso significa que, para o alinhamento vertical, cada `Label` ocupa a mesma quantidade de espaço dentro do `StackLayout`. No entanto, somente o último `Label`, que define sua propriedade [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) tem um tamanho diferente.

    > [!IMPORTANT]
    > Quando todo o espaço em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) é usado, as preferências de expansão não têm nenhum efeito.

    Para obter mais informações sobre alinhamento e expansão, confira [Opções de layout no Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`StackLayout`](xref:Xamarin.Forms.StackLayout) para definir opções de alinhamento e expansão para cada [`Label`](xref:Xamarin.Forms.Label):

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Esse código define preferências de alinhamento nas primeiras quatro instâncias de [`Label`](xref:Xamarin.Forms.Label), bem como preferências de expansão nas quatro últimas instâncias de `Label`. Os campos [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) e [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) são usados para definir o alinhamento do das instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) pai. Os campos [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) e [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) são usados para definir a preferência de alinhamento e definir se a exibição ocupará mais espaço, se disponível, dentro do `StackLayout` pai.

    > [!NOTE]
    > O valor padrão das propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de uma exibição é [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de exibições filho em um StackLayout, com opções de alinhamento e expansão definidas, no iOS e no Android](../images/alignment-expansion.png "StackLayout contendo instâncias de rótulo, com alinhamento e expansão definidos")](../images/alignment-expansion-large.png#lightbox "StackLayout contendo instâncias de rótulo, com alinhamento e expansão definidos")

    Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) respeita somente as preferências de alinhamento em exibições filho que estão na direção oposta à orientação `StackLayout`. Portanto, as exibições filho de [`Label`](xref:Xamarin.Forms.Label) dentro do `StackLayout` orientado verticalmente definem suas propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) como um dos campos de alinhamento:

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), que posiciona o [`Label`](xref:Xamarin.Forms.Label) à esquerda do [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), que centraliza o [`Label`](xref:Xamarin.Forms.Label) no [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), que posiciona o [`Label`](xref:Xamarin.Forms.Label) à direita do [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), que garante que o [`Label`](xref:Xamarin.Forms.Label) preencha a largura do [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) só pode expandir exibições filho na direção de sua orientação. Portanto, o `StackLayout` orientado verticalmente pode expandir exibições filho de [`Label`](xref:Xamarin.Forms.Label) que definem suas propriedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como um dos campos de expansão. Isso significa que, para o alinhamento vertical, cada `Label` ocupa a mesma quantidade de espaço dentro do `StackLayout`. No entanto, somente o último `Label`, que define sua propriedade [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) tem um tamanho diferente.

    > [!IMPORTANT]
    > Quando todo o espaço em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) é usado, as preferências de expansão não têm nenhum efeito.

    Para obter mais informações sobre alinhamento e expansão, confira [Opções de layout no Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md).
