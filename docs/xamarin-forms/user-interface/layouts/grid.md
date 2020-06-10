---
Título: Descrição de " Xamarin.Forms grade": "a Xamarin.Forms grade é um layout que organiza seus filhos em linhas e colunas de células".
MS. Prod: xamarin MS. AssetID: 762B1802-D185-494C-B643-74EED55882FE MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 05/15/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-grid"></a>Xamarin.FormsGrade

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![Xamarin.FormsGrade](grid-images/layouts.png "[! Parar. Grade não-LOC (Xamarin. Forms)]")](grid-images/layouts-large.png#lightbox "[! Parar. Grade não-LOC (Xamarin. Forms)]")

O [`Grid`](xref:Xamarin.Forms.Grid) é um layout que organiza seus filhos em linhas e colunas, que podem ter tamanhos proporcionais ou absolutos. Por padrão, um `Grid` contém uma linha e uma coluna. Além disso, um `Grid` pode ser usado como um layout pai que contém outros layouts filho.

O [`Grid`](xref:Xamarin.Forms.Grid) layout não deve ser confundido com tabelas e não tem o objetivo de apresentar dados tabulares. Ao contrário das tabelas HTML, um `Grid` é destinado a dispor conteúdo. Para exibir dados tabulares, considere usar [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)ou [Tableview](~/xamarin-forms/user-interface/tableview.md).

A [`Grid`](xref:Xamarin.Forms.Grid) classe define as seguintes propriedades:

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), do tipo `int` , que é uma propriedade anexada que indica o alinhamento de coluna de uma exibição dentro de um pai `Grid` . O valor padrão dessa propriedade é 0. Um retorno de chamada de validação garante que, quando a propriedade for definida, seu valor seja maior ou igual a 0.
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions), do tipo [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) , é uma lista de [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objetos que definem a largura das colunas de grade.
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing), do tipo `double` , indica a distância entre as colunas da grade. O valor padrão dessa propriedade é 6 unidades independentes de dispositivo.
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty), do tipo `int` , que é uma propriedade anexada que indica o número total de colunas que uma exibição abrange dentro de um pai `Grid` . O valor padrão desta propriedade é 1. Um retorno de chamada de validação garante que, quando a propriedade for definida, seu valor seja maior ou igual a 1.
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty), do tipo `int` , que é uma propriedade anexada que indica o alinhamento de linha de uma exibição dentro de um pai `Grid` . O valor padrão dessa propriedade é 0. Um retorno de chamada de validação garante que, quando a propriedade for definida, seu valor seja maior ou igual a 0.
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), do tipo [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) , é uma lista de [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) objetos que definem a altura das linhas de grade.
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing), do tipo `double` , indica a distância entre as linhas de grade. O valor padrão dessa propriedade é 6 unidades independentes de dispositivo.
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty), do tipo `int` , que é uma propriedade anexada que indica o número total de linhas que uma exibição abrange dentro de um pai `Grid` . O valor padrão desta propriedade é 1. Um retorno de chamada de validação garante que, quando a propriedade for definida, seu valor seja maior ou igual a 1.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados e com estilo.

A [`Grid`](xref:Xamarin.Forms.Grid) classe deriva da `Layout<T>` classe, que define uma `Children` Propriedade do tipo `IList<T>` . A `Children` propriedade é a `ContentProperty` da `Layout<T>` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

> [!TIP]
> Para obter o melhor desempenho de layout possível, siga as diretrizes em [otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="rows-and-columns"></a>Linhas e colunas

Por padrão, um [`Grid`](xref:Xamarin.Forms.Grid) contém uma linha e uma coluna:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

Neste exemplo, o [`Grid`](xref:Xamarin.Forms.Grid) contém um único filho [`Label`](xref:Xamarin.Forms.Label) que é posicionado automaticamente em um único local:

[![Captura de tela de um layout de grade padrão](grid-images/default.png "Layout de grade padrão")](grid-images/default-large.png#lightbox "Layout de grade padrão")

O comportamento de layout de um [`Grid`](xref:Xamarin.Forms.Grid) pode ser definido com [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) as [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) Propriedades e, que são coleções [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) de [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objetos e, respectivamente. Essas coleções definem as características de linha e coluna de um `Grid` e devem conter um [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objeto para cada linha no `Grid` e um [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objeto para cada coluna no `Grid` .

A [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) classe define uma [`Height`](xref:Xamarin.Forms.RowDefinition.Height) propriedade, do tipo [`GridLength`](xref:Xamarin.Forms.GridLength) , e a [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) classe define uma [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propriedade, do tipo [`GridLength`](xref:Xamarin.Forms.GridLength) . A [`GridLength`](xref:Xamarin.Forms.GridLength) struct especifica uma altura de linha ou uma largura de coluna em termos da [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) enumeração, que tem três membros:

- `Absolute`– a altura da linha ou a largura da coluna é um valor nas unidades independentes do dispositivo (um número em XAML).
- `Auto`– a altura da linha ou a largura da coluna é autodimensionada com base no conteúdo da célula ( `Auto` em XAML).
- `Star`– a altura da linha ou a largura da coluna de sobra é alocada proporcionalmente (um número seguido de `*` em XAML).

Uma [`Grid`](xref:Xamarin.Forms.Grid) linha com uma `Height` propriedade de `Auto` restringe a altura das exibições nessa linha da mesma maneira que uma vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Da mesma forma, uma coluna com uma `Width` propriedade de funciona de maneira `Auto` muito semelhante a uma horizontal `StackLayout` .

> [!CAUTION]
> Tente garantir que o menor número possível de linhas e colunas seja definido como [`Auto`](xref:Xamarin.Forms.GridLength.Auto) tamanho. Cada linha ou coluna dimensionada automaticamente fará o mecanismo de layout realizar cálculos de layout adicionais. Em vez disso, use linhas e colunas de tamanho fixo, se possível. Como alternativa, defina linhas e colunas para ocupar uma quantidade proporcional de espaço com o valor de [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) enumeração.

O XAML a seguir mostra como criar um [`Grid`](xref:Xamarin.Forms.Grid) com três linhas e duas colunas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Neste exemplo, o [`Grid`](xref:Xamarin.Forms.Grid) tem uma altura geral que é a altura da página. O `Grid` sabe que a altura da terceira linha é de 100 unidades independentes de dispositivo. Ele subtrai essa altura de sua própria altura e aloca a altura restante proporcionalmente entre a primeira e a segunda linhas com base no número antes da estrela. Neste exemplo, a altura da primeira linha é duas vezes a da segunda linha.

Os dois [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objetos definem o [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) como `*` , que é o mesmo que `1*` , o que significa que a largura da tela é dividida igualmente abaixo das duas colunas.

> [!IMPORTANT]
> O valor padrão da [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) propriedade é `*` . Da mesma forma, o valor padrão da [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propriedade é `*` . Portanto, não é necessário definir essas propriedades nos casos em que esses padrões são aceitáveis.

As exibições filhas podem ser posicionadas em [`Grid`](xref:Xamarin.Forms.Grid) células específicas com as [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) Propriedades anexadas e. Além disso, para fazer com que as exibições filhas se estendam por várias linhas e colunas, use as [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) Propriedades anexadas e.

O XAML a seguir mostra a mesma [`Grid`](xref:Xamarin.Forms.Grid) definição e também posiciona os modos de exibição filho em `Grid` células específicas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <BoxView Color="Green" />
        <Label Text="Row 0, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Column="1"
                 Color="Blue" />
        <Label Grid.Column="1"
               Text="Row 0, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Color="Teal" />
        <Label Grid.Row="1"
               Text="Row 1, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="Purple" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Row1, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Grid.ColumnSpan="2"
                 Color="Red" />
        <Label Grid.Row="2"
               Grid.ColumnSpan="2"
               Text="Row 2, Columns 0 and 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

> [!NOTE]
> As `Grid.Row` `Grid.Column` Propriedades e são indexadas de 0 e, portanto, `Grid.Row="2"` referem-se à terceira linha enquanto `Grid.Column="1"` se refere à segunda coluna. Além disso, ambas as propriedades têm um valor padrão de 0 e, portanto, não precisam ser definidas em exibições filho que ocupem a primeira linha ou primeira coluna de a [`Grid`](xref:Xamarin.Forms.Grid) .

Neste exemplo, todas as três [`Grid`](xref:Xamarin.Forms.Grid) linhas são ocupadas [`BoxView`](xref:Xamarin.Forms.BoxView) por [`Label`](xref:Xamarin.Forms.Label) exibições e. A terceira linha tem 100 unidades independentes de dispositivo alta, com as duas primeiras linhas ocupando o espaço restante (a primeira linha é duas vezes maior que a segunda linha). As duas colunas são iguais na largura e dividem a `Grid` metade. O `BoxView` na terceira linha abrange ambas as colunas.

[![Captura de tela de um layout de grade básico](grid-images/basic.png "Layout de grade básico")](grid-images/basic-large.png#lightbox "Layout de grade básico")

Além disso, as exibições filhas em um [`Grid`](xref:Xamarin.Forms.Grid) podem compartilhar células. A ordem em que os filhos aparecem no XAML é a ordem em que os filhos são colocados no `Grid` . No exemplo anterior, os [`Label`](xref:Xamarin.Forms.Label) objetos só são visíveis porque eles são renderizados sobre os [`BoxView`](xref:Xamarin.Forms.BoxView) objetos. Os `Label` objetos não ficarão visíveis se os `BoxView` objetos fossem renderizados sobre eles.

Este é o código C# equivalente:

```csharp
public class BasicGridPageCS : ContentPage
{
    public BasicGridPageCS()
    {
        Grid grid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition { Height = new GridLength(2, GridUnitType.Star) },
                new RowDefinition(),
                new RowDefinition { Height = new GridLength(100) }
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        // The BoxView and Label are in row 0 and column 0, and so only needs to be added to the
        // Grid.Children collection to get default row and column settings.
        grid.Children.Add(new BoxView
        {
            Color = Color.Green
        });
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        });

        // This BoxView and Label are in row 0 and column 1, which are specified as arguments
        // to the Add method.
        grid.Children.Add(new BoxView
        {
            Color = Color.Blue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 0);

        // Row 1
        // This BoxView and Label are in row 1 and column 0, which are specified as arguments
        // to the Add method overload.
        grid.Children.Add(new BoxView
        {
            Color = Color.Teal
        }, 0, 1, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row 1, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1, 1, 2); // These arguments indicate that that the child element goes in the column starting at 0 but ending before 1.
                        // They also indicate that the child element goes in the row starting at 1 but ending before 2.

        grid.Children.Add(new BoxView
        {
            Color = Color.Purple
        }, 1, 2, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row1, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 2, 1, 2);

        // Row 2
        // Alternatively, the BoxView and Label can be positioned in cells with the Grid.SetRow
        // and Grid.SetColumn methods.
        BoxView boxView = new BoxView { Color = Color.Red };
        Grid.SetRow(boxView, 2);
        Grid.SetColumnSpan(boxView, 2);
        Label label = new Label
        {
            Text = "Row 2, Column 0 and 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        };
        Grid.SetRow(label, 2);
        Grid.SetColumnSpan(label, 2);

        grid.Children.Add(boxView);
        grid.Children.Add(label);

        Title = "Basic Grid demo";
        Content = grid;
    }
}
```

No código, para especificar a altura de um [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objeto e a largura de um [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objeto, você usa valores da [`GridLength`](xref:Xamarin.Forms.GridLength) estrutura, geralmente em combinação com a [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) enumeração.

O código de exemplo acima também mostra várias abordagens diferentes para adicionar filhos ao [`Grid`](xref:Xamarin.Forms.Grid) e especificar as células nas quais eles residem. Ao usar a `Add` sobrecarga que especifica os argumentos *esquerdo*, *direito*, *superior*e *inferior* , enquanto os argumentos *esquerdo* e *superior* sempre se referem a células dentro do `Grid` , os argumentos *direito* e *inferior* parecem se referir a células que estão fora do `Grid` . Isso ocorre porque o argumento *à direita* sempre deve ser maior que o argumento *esquerdo* , e o argumento *inferior* deve ser sempre maior que o argumento *superior* . O exemplo a seguir, que pressupõe um 2x2 `Grid` , mostra o código equivalente usando as duas `Add` sobrecargas:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);           // first column, first row
grid.Children.Add(topRight, 1, 0);          // second column, first tow
grid.Children.Add(bottomLeft, 0, 1);        // first column, second row
grid.Children.Add(bottomRight, 1, 1);       // second column, second row

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);     // first column, first row
grid.Children.Add(topRight, 1, 2, 0, 1);    // second column, first tow
grid.Children.Add(bottomLeft, 0, 1, 1, 2);  // first column, second row
grid.Children.Add(bottomRight, 1, 2, 1, 2); // second column, second row
```

> [!NOTE]
> Além disso, modos de exibição filho podem ser adicionados a um [`Grid`](xref:Xamarin.Forms.Grid) com os [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) métodos e, que adicionam filhos a uma única linha ou a uma única coluna `Grid` . O `Grid` , em seguida, é expandido em linhas ou colunas conforme essas chamadas são feitas, bem como posicionar filhos automaticamente nas células corretas.

## <a name="space-between-rows-and-columns"></a>Espaço entre linhas e colunas

Por padrão, as [`Grid`](xref:Xamarin.Forms.Grid) linhas são separadas por 6 unidades de espaço independentes de dispositivo. Da mesma forma, as `Grid` colunas são separadas por 6 unidades de espaço independentes de dispositivo. Esses padrões podem ser alterados definindo as [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) Propriedades e, respectivamente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridSpacingPage"
             Title="Grid spacing demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        ..
    </Grid>
</ContentPage>
```

Este exemplo cria um [`Grid`](xref:Xamarin.Forms.Grid) que não tem nenhum espaçamento entre suas linhas e colunas:

[![Captura de tela da grade sem espaçamento entre células](grid-images/spacing.png "Grade sem espaçamento entre células")](grid-images/spacing-large.png#lightbox "Grade sem espaçamento entre células")

> [!TIP]
> As [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) Propriedades e podem ser definidas para valores negativos para tornar o conteúdo da célula sobreposto.

Este é o código C# equivalente:

```csharp
public GridSpacingPageCS()
{
    Grid grid = new Grid
    {
        RowSpacing = 0,
        ColumnSpacing = 0,
        // ...
    };
    // ...

    Content = grid;
}
```

## <a name="alignment"></a>Alinhamento

As exibições filhas em um [`Grid`](xref:Xamarin.Forms.Grid) podem ser posicionadas em suas células pelas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e. Essas propriedades podem ser definidas para os seguintes campos da [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> Os `AndExpands` campos na [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura só são aplicáveis a [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos.

O XAML a seguir cria um [`Grid`](xref:Xamarin.Forms.Grid) com nove células de tamanho igual e coloca um [`Label`](xref:Xamarin.Forms.Label) em cada célula com um alinhamento diferente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridAlignmentPage"
             Title="Grid alignment demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <BoxView Color="AliceBlue" />
        <Label Text="Upper left"
               HorizontalOptions="Start"
               VerticalOptions="Start" />
        <BoxView Grid.Column="1"
                 Color="LightSkyBlue" />
        <Label Grid.Column="1"
               Text="Upper center"
               HorizontalOptions="Center"
               VerticalOptions="Start"/>
        <BoxView Grid.Column="2"
                 Color="CadetBlue" />
        <Label Grid.Column="2"
               Text="Upper right"
               HorizontalOptions="End"
               VerticalOptions="Start" />
        <BoxView Grid.Row="1"
                 Color="CornflowerBlue" />
        <Label Grid.Row="1"
               Text="Center left"
               HorizontalOptions="Start"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="DodgerBlue" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Center center"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="2"
                 Color="DarkSlateBlue" />
        <Label Grid.Row="1"
               Grid.Column="2"
               Text="Center right"
               HorizontalOptions="End"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Color="SteelBlue" />
        <Label Grid.Row="2"
               Text="Lower left"
               HorizontalOptions="Start"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="1"
                 Color="LightBlue" />
        <Label Grid.Row="2"
               Grid.Column="1"
               Text="Lower center"
               HorizontalOptions="Center"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="2"
                 Color="BlueViolet" />
        <Label Grid.Row="2"
               Grid.Column="2"
               Text="Lower right"
               HorizontalOptions="End"
               VerticalOptions="End" />
    </Grid>
</ContentPage>
```

Neste exemplo, os [`Label`](xref:Xamarin.Forms.Label) objetos em cada linha são todos alinhados de forma idêntica verticalmente, mas usam alinhamentos horizontais diferentes. Como alternativa, isso pode ser considerado como os `Label` objetos em cada coluna sendo alinhados de forma idêntica horizontal, mas usando alinhamentos verticais diferentes:

[![Captura de tela de alinhamento de célula dentro de uma grade](grid-images/alignment.png "Alinhamento de célula em uma grade")](grid-images/alignment-large.png#lightbox "Alinhamento de célula em uma grade")

Este é o código C# equivalente:

```csharp
public class GridAlignmentPageCS : ContentPage
{
    public GridAlignmentPageCS()
    {
        Grid grid = new Grid
        {
            RowSpacing = 0,
            ColumnSpacing = 0,
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        grid.Children.Add(new BoxView
        {
            Color = Color.AliceBlue
        });
        grid.Children.Add(new Label
        {
            Text = "Upper left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        });

        grid.Children.Add(new BoxView
        {
            Color = Color.LightSkyBlue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Start
        }, 1, 0);

        grid.Children.Add(new BoxView
        {
            Color = Color.CadetBlue
        }, 2, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Start
        }, 2, 0);

        // Row 1
        grid.Children.Add(new BoxView
        {
            Color = Color.CornflowerBlue
        }, 0, 1);
        grid.Children.Add(new Label
        {
            Text = "Center left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DodgerBlue
        }, 1, 1);
        grid.Children.Add(new Label
        {
            Text = "Center center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DarkSlateBlue
        }, 2, 1);
        grid.Children.Add(new Label
        {
            Text = "Center right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Center
        }, 2, 1);

        // Row 2
        grid.Children.Add(new BoxView
        {
            Color = Color.SteelBlue
        }, 0, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.End
        }, 0, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.LightBlue
        }, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.End
        }, 1, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.BlueViolet
        }, 2, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.End
        }, 2, 2);

        Title = "Grid alignment demo";
        Content = grid;
    }
}
```

## <a name="nested-grid-objects"></a>Objetos de grade aninhados

Um [`Grid`](xref:Xamarin.Forms.Grid) pode ser usado como um layout pai que contém objetos filho aninhados `Grid` ou outros layouts filho. Ao aninhar `Grid` objetos, as `Grid.Row` Propriedades,, `Grid.Column` `Grid.RowSpan` e `Grid.ColumnSpan` anexadas sempre se referem à posição das exibições dentro de seu pai `Grid` .

O XAML a seguir mostra um exemplo de aninhamento de [`Grid`](xref:Xamarin.Forms.Grid) objetos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:converters="clr-namespace:GridDemos.Converters"
             x:Class="GridDemos.Views.ColorSlidersGridPage"
             Title="Nested Grids demo">

    <ContentPage.Resources>
        <converters:DoubleToIntConverter x:Key="doubleToInt" />

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment"
                    Value="Center" />
        </Style>
    </ContentPage.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <BoxView x:Name="boxView"
                 Color="Black" />
        <Grid Grid.Row="1"
              Margin="20">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Slider x:Name="redSlider"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="1"
                   Text="{Binding Source={x:Reference redSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0}'}" />
            <Slider x:Name="greenSlider"
                    Grid.Row="2"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="3"
                   Text="{Binding Source={x:Reference greenSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0}'}" />
            <Slider x:Name="blueSlider"
                    Grid.Row="4"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="5"
                   Text="{Binding Source={x:Reference blueSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Blue = {0}'}" />
        </Grid>
    </Grid>
</ContentPage>
```

Neste exemplo, o layout raiz [`Grid`](xref:Xamarin.Forms.Grid) contém um [`BoxView`](xref:Xamarin.Forms.BoxView) em sua primeira linha e um filho `Grid` em sua segunda linha. O filho `Grid` contém [`Slider`](xref:Xamarin.Forms.Slider) objetos que manipulam a cor exibida pelos `BoxView` objetos e [`Label`](xref:Xamarin.Forms.Label) que exibem o valor de cada um `Slider` :

[![Captura de tela de grades aninhadas](grid-images/nesting.png "Objetos de grade aninhados")](grid-images/nesting-large.png#lightbox "Objetos de grade aninhados")

> [!IMPORTANT]
> Quanto mais fundo você aninhar [`Grid`](xref:Xamarin.Forms.Grid) objetos e outros layouts, mais os layouts aninhados afetarão o desempenho. Para obter mais informações, consulte [escolher o layout correto](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

Este é o código C# equivalente:

```csharp
public class ColorSlidersGridPageCS : ContentPage
{
    BoxView boxView;
    Slider redSlider;
    Slider greenSlider;
    Slider blueSlider;

    public ColorSlidersGridPageCS()
    {
        // Create an implicit style for the Labels
        Style labelStyle = new Style(typeof(Label))
        {
            Setters =
            {
                new Setter { Property = Label.HorizontalTextAlignmentProperty, Value = TextAlignment.Center }
            }
        };
        Resources.Add(labelStyle);

        // Root page layout
        Grid rootGrid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition()
            }
        };

        boxView = new BoxView { Color = Color.Black };
        rootGrid.Children.Add(boxView);

        // Child page layout
        Grid childGrid = new Grid
        {
            Margin = new Thickness(20),
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            }
        };

        DoubleToIntConverter doubleToInt = new DoubleToIntConverter();

        redSlider = new Slider();
        redSlider.ValueChanged += OnSliderValueChanged;
        childGrid.Children.Add(redSlider);

        Label redLabel = new Label();
        redLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Red = {0}", source: redSlider));
        Grid.SetRow(redLabel, 1);
        childGrid.Children.Add(redLabel);

        greenSlider = new Slider();
        greenSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(greenSlider, 2);
        childGrid.Children.Add(greenSlider);

        Label greenLabel = new Label();
        greenLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Green = {0}", source: greenSlider));
        Grid.SetRow(greenLabel, 3);
        childGrid.Children.Add(greenLabel);

        blueSlider = new Slider();
        blueSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(blueSlider, 4);
        childGrid.Children.Add(blueSlider);

        Label blueLabel = new Label();
        blueLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Blue = {0}", source: blueSlider));
        Grid.SetRow(blueLabel, 5);
        childGrid.Children.Add(blueLabel);

        // Place the child Grid in the root Grid
        rootGrid.Children.Add(childGrid, 0, 1);

        Title = "Nested Grids demo";
        Content = rootGrid;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        boxView.Color = new Color(redSlider.Value, greenSlider.Value, blueSlider.Value);
    }
}
```

## <a name="related-links"></a>Links relacionados

- [Demonstrações em grade (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [Opções de layout emXamarin.Forms](layout-options.md)
- [Escolher um Xamarin.Forms layout](choose-layout.md)
- [Melhorar o Xamarin.Forms desempenho do aplicativo](~/xamarin-forms/deploy-test/performance.md)
