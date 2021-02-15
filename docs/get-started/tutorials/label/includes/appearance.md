---
ms.openlocfilehash: 8b40b710b38a82c680f2da86f4d89b34b9ef00e4
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689779"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Label`](xref:Xamarin.Forms.Label) para alterar sua aparência visual:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Esse código define propriedades que alteram a aparência visual do [`Label`](xref:Xamarin.Forms.Label). A propriedade [`TextColor`](xref:Xamarin.Forms.Label.TextColor) define a cor do texto `Label`. A propriedade [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) define a fonte do rótulo como itálico e a propriedade [`FontSize`](xref:Xamarin.Forms.Label.FontSize) define o tamanho da fonte. Além disso, uma decoração de texto sublinhado é aplicada ao `Label` definindo sua propriedade [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) e ele é centralizado horizontalmente definindo a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) como [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador remoto de iOS ou do Android Emulator de sua preferência. Observe que a aparência de [`Label`](xref:Xamarin.Forms.Label) mudou:

    [![Captura de tela de um rótulo com uma aparência visual alterada, no iOS e no Android](../images/change-label-appearance.png "Rótulo com aparência alterada")](../images/change-label-appearance-large.png#lightbox "Rótulo com aparência alterada")

    Para obter mais informações sobre como definir a aparência de [`Label`](xref:Xamarin.Forms.Label), confira o guia [Rótulo do Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Label`](xref:Xamarin.Forms.Label) para alterar sua aparência visual:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Esse código define propriedades que alteram a aparência visual do [`Label`](xref:Xamarin.Forms.Label). A propriedade [`TextColor`](xref:Xamarin.Forms.Label.TextColor) define a cor do texto `Label`. A propriedade [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) define a fonte do rótulo como itálico e a propriedade [`FontSize`](xref:Xamarin.Forms.Label.FontSize) define o tamanho da fonte. Além disso, uma decoração de texto sublinhado é aplicada ao `Label` definindo sua propriedade [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) e ele é centralizado horizontalmente definindo a propriedade [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) como [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. Caso o aplicativo ainda esteja em execução, salve as alterações no arquivo. Dessa maneira, a interface do usuário do aplicativo será atualizada de modo automático em seu simulador ou emulador. Caso contrário, na barra de ferramentas do Visual Studio para Mac, clique no botão **Iniciar** (um botão triangular semelhante ao botão Reproduzir) para executar a inicialização do aplicativo dentro do simulador de iOS ou do Android Emulator de sua preferência. Observe que a aparência de [`Label`](xref:Xamarin.Forms.Label) mudou:

    [![Captura de tela de um rótulo com uma aparência visual alterada, no iOS e no Android](../images/change-label-appearance.png "Rótulo com aparência alterada")](../images/change-label-appearance-large.png#lightbox "Rótulo com aparência alterada")

    Para obter mais informações sobre como definir a aparência de [`Label`](xref:Xamarin.Forms.Label), confira o guia [Rótulo do Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
