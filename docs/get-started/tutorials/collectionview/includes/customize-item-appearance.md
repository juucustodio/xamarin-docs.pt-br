---
ms.openlocfilehash: 6118d4ee8d2fa56a3b14d3d280db4991a36874fc
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690151"
---
Anteriormente, o [`CollectionView`](xref:Xamarin.Forms.CollectionView) era preenchido com os dados usando a associação de dados. No entanto, apesar da associação de dados a uma coleção, em que cada objeto na coleção definiu vários itens de dados, apenas um único item de dados foi exibido por objeto (a propriedade `Name` do objeto `Monkey`).

Neste exercício, você modificará o projeto **CollectionViewTutorial** para que o [`CollectionView`](xref:Xamarin.Forms.CollectionView) exiba vários itens de dados em cada linha.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Em **MainPage.xaml**, modifique a declaração [`CollectionView`](xref:Xamarin.Forms.CollectionView) para personalizar a aparência de cada item de dados:

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
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
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    Esse código define a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada item na [`CollectionView`](xref:Xamarin.Forms.CollectionView). O filho do `DataTemplate` é um [`Grid`](xref:Xamarin.Forms.Grid), que contém um objeto [`Image`](xref:Xamarin.Forms.Image) e dois objetos [`Label`](xref:Xamarin.Forms.Label). Os dados de objeto `Image` associam sua propriedade [`Source`](xref:Xamarin.Forms.Image.Source) à propriedade `ImageUrl` de cada objeto `Monkey`, enquanto os objetos `Label` associam suas propriedades [`Text`](xref:Xamarin.Forms.Label.Text) às propriedades `Name` e `Location` de cada objeto `Monkey`.

    Para obter mais informações sobre a aparência do item do [`CollectionView`](xref:Xamarin.Forms.CollectionView), confira [Definir a aparência do item](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance). Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador remoto de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma CollectionView cujos itens são modelados com um modelo de dados](../images/customize-item-appearance.png "CollectionView exibindo dados modelados")](../images/customize-item-appearance-large.png#lightbox "CollectionView exibindo dados modelados")

    Interrompa o aplicativo no Visual Studio.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Em **MainPage.xaml**, modifique a declaração [`CollectionView`](xref:Xamarin.Forms.CollectionView) para personalizar a aparência de cada item de dados:

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
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
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    Esse código define a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada item na [`CollectionView`](xref:Xamarin.Forms.CollectionView). O filho do `DataTemplate` é um [`Grid`](xref:Xamarin.Forms.Grid), que contém um objeto [`Image`](xref:Xamarin.Forms.Image) e dois objetos [`Label`](xref:Xamarin.Forms.Label). Os dados de objeto `Image` associam sua propriedade [`Source`](xref:Xamarin.Forms.Image.Source) à propriedade `ImageUrl` de cada objeto `Monkey`, enquanto os objetos `Label` associam suas propriedades [`Text`](xref:Xamarin.Forms.Label.Text) às propriedades `Name` e `Location` de cada objeto `Monkey`.

    Para obter mais informações sobre a aparência do item do [`CollectionView`](xref:Xamarin.Forms.CollectionView), confira [Definir a aparência do item](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance). Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido:

    [![Captura de tela de uma CollectionView cujos itens são modelados com um modelo de dados](../images/customize-item-appearance.png "CollectionView exibindo dados modelados")](../images/customize-item-appearance-large.png#lightbox "CollectionView exibindo dados modelados")

    Interrompa o aplicativo no Visual Studio para Mac.
