---
ms.openlocfilehash: c826ee87c006b05322af8c9312bdf3120df8b357
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "61375329"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`Grid`](xref:Xamarin.Forms.Grid) para definir colunas e linhas e coloque o conteúdo que abrange as colunas e linhas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    Esse código define as colunas e linhas para a [`Grid`](xref:Xamarin.Forms.Grid) e posiciona as instâncias de [`Label`](xref:Xamarin.Forms.Label) em linhas e colunas específicas. O primeiro `Label` define a propriedade [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) anexada para que seu texto se estenda por várias colunas. A propriedade `ColumnSpan` é definida como 2, o que representa o número de colunas que `Label` abrangerá. O segundo `Label` define a propriedade [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) anexada para que seu texto abranja várias linhas. A propriedade `RowSpan` é definida como 2, o que representa o número de linhas que `Label` abrangerá.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS remoto ou Android Emulator escolhido:

    [![Captura de tela de uma grade que tem conteúdo que abrange várias colunas e linhas, no iOS e no Android](../images/span-columns-rows.png "Grade com conteúdo que abrange colunas e linhas")](../images/span-columns-rows-large.png#lightbox "Grade com conteúdo que abrange colunas e linhas")

    Para obter mais informações sobre a abrangência de colunas e linhas, confira [Abrangências](~/xamarin-forms/user-interface/layouts/grid.md#spans) no guia de [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`Grid`](xref:Xamarin.Forms.Grid) para definir colunas e linhas e coloque o conteúdo que abrange as colunas e linhas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    Esse código define as colunas e linhas para a [`Grid`](xref:Xamarin.Forms.Grid) e posiciona as instâncias de [`Label`](xref:Xamarin.Forms.Label) em linhas e colunas específicas. O primeiro `Label` define a propriedade [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) anexada para que seu texto se estenda por várias colunas. A propriedade `ColumnSpan` é definida como 2, o que representa o número de colunas que `Label` abrangerá. O segundo `Label` define a propriedade [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) anexada para que seu texto abranja várias linhas. A propriedade `RowSpan` é definida como 2, o que representa o número de linhas que `Label` abrangerá.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma grade que tem conteúdo que abrange várias colunas e linhas, no iOS e no Android](../images/span-columns-rows.png "Grade com conteúdo que abrange colunas e linhas")](../images/span-columns-rows-large.png#lightbox "Grade com conteúdo que abrange colunas e linhas")

    Para obter mais informações sobre a abrangência de colunas e linhas, confira [Abrangências](~/xamarin-forms/user-interface/layouts/grid.md#spans) no guia de [Grade do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
