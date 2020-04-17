---
ms.openlocfilehash: 04d2a244482c92a615c3eec2130cec1df8aaaf1f
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81389941"
---
Anteriormente, o [`ListView`](xref:Xamarin.Forms.ListView) era preenchido com os dados usando a associação de dados. No entanto, apesar da associação de dados a uma coleção, em que cada objeto na coleção definiu vários itens de dados, apenas um único item de dados foi exibido por objeto (a propriedade `Name` do objeto `Monkey`).

Neste exercício, você modificará o projeto **ListViewTutorial** para que o [`ListView`](xref:Xamarin.Forms.ListView) exiba vários itens de dados em cada linha.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Na **MainPage.xaml**, modifique a declaração [`ListView`](xref:Xamarin.Forms.ListView) para personalizar a aparência de cada linha:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Esse código define a propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada linha no [`ListView`](xref:Xamarin.Forms.ListView). O filho do `DataTemplate` deve ser do tipo [`Cell`](xref:Xamarin.Forms.Cell) ou derivado dele. Esse código usa uma [`ViewCell`](xref:Xamarin.Forms.ViewCell), que é derivada de `Cell`, para criar um layout personalizado para cada linha `ListView`. O layout dentro de `ViewCell` é gerenciado aqui por um [`Grid`](xref:Xamarin.Forms.Grid), que contém um objeto [`Image`](xref:Xamarin.Forms.Image) e dois objetos [`Label`](xref:Xamarin.Forms.Label). Os dados de objeto `Image` associam sua propriedade [`Source`](xref:Xamarin.Forms.Image.Source) à propriedade `ImageUrl` de cada objeto `Monkey`, enquanto os objetos `Label` associam suas propriedades [`Text`](xref:Xamarin.Forms.Label.Text) às propriedades `Name` e `Location` de cada objeto `Monkey`.

    Por padrão, todas as linhas em uma [`ListView`](xref:Xamarin.Forms.ListView) têm a mesma altura. No entanto, esse código define a propriedade [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) como `true`, de modo que a altura da linha pode ser dimensionada para seu conteúdo. Isso acomoda as propriedades `Name` e `Location` de cada objeto `Monkey` com cadeia de caracteres de comprimento variável.

    Para obter mais informações sobre [`ListView`](xref:Xamarin.Forms.ListView) a aparência da célula, confira [Personalizando a aparência da célula ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma ListView cujos itens são modelados com um modelo de dados](../images/customize-cell-appearance.png "ListView exibindo dados modelados")](../images/customize-cell-appearance-large.png#lightbox "ListView exibindo dados modelados")

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Na **MainPage.xaml**, modifique a declaração [`ListView`](xref:Xamarin.Forms.ListView) para personalizar a aparência de cada linha:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Esse código define a propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada linha no [`ListView`](xref:Xamarin.Forms.ListView). O filho do `DataTemplate` deve ser do tipo [`Cell`](xref:Xamarin.Forms.Cell) ou derivado dele. Esse código usa uma [`ViewCell`](xref:Xamarin.Forms.ViewCell), que é derivada de `Cell`, para criar um layout personalizado para cada linha `ListView`. O layout dentro de `ViewCell` é gerenciado aqui por um [`Grid`](xref:Xamarin.Forms.Grid), que contém um objeto [`Image`](xref:Xamarin.Forms.Image) e dois objetos [`Label`](xref:Xamarin.Forms.Label). Os dados de objeto `Image` associam sua propriedade [`Source`](xref:Xamarin.Forms.Image.Source) à propriedade `ImageUrl` de cada objeto `Monkey`, enquanto os objetos `Label` associam suas propriedades [`Text`](xref:Xamarin.Forms.Label.Text) às propriedades `Name` e `Location` de cada objeto `Monkey`.

    Por padrão, todas as linhas em uma [`ListView`](xref:Xamarin.Forms.ListView) têm a mesma altura. No entanto, esse código define a propriedade [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) como `true`, de modo que a altura da linha pode ser dimensionada para seu conteúdo. Isso acomoda as propriedades `Name` e `Location` de cada objeto `Monkey` com cadeia de caracteres de comprimento variável.

    Para obter mais informações sobre [`ListView`](xref:Xamarin.Forms.ListView) a aparência da célula, confira [Personalizando a aparência da célula ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma ListView cujos itens são modelados com um modelo de dados](../images/customize-cell-appearance.png "ListView exibindo dados modelados")](../images/customize-cell-appearance-large.png#lightbox "ListView exibindo dados modelados")
