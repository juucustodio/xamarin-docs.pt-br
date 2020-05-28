---
title: Xamarin.FormsBoxView
description: Este artigo explica como usar um retângulo colorido para decoração, gráficos e interação em um Xamarin.Forms aplicativo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5f915955bff969ef38cdb7a89bf9cecf05401131
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136351"
---
# <a name="xamarinforms-boxview"></a>Xamarin.FormsBoxView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView)renderiza um retângulo simples de uma largura, altura e cor especificadas. Você pode usar `BoxView` para decoração, gráficos rudimentares e para interação com o usuário por meio de toque.

Como Xamarin.Forms o não tem um sistema gráfico vetorial interno, o ajuda a `BoxView` compensar. Alguns dos programas de exemplo descritos neste artigo usam `BoxView` para o processamento de gráficos. O `BoxView` pode ser dimensionado para se assemelhar a uma linha de largura e espessura específica e, em seguida, girado por qualquer ângulo usando a `Rotation` propriedade.

Embora o `BoxView` possa imitar gráficos simples, talvez você queira investigar [usando o Xamarin.Forms SkiaSharp no](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) para obter requisitos gráficos mais sofisticados.

Este artigo discute os seguintes tópicos:

- **[Definindo a cor e o tamanho](#colorandsize)** &ndash; do BoxView Defina as `BoxView` Propriedades.
- **[Renderizando decorações](#textdecorations)** &ndash; de texto Use um `BoxView` para processar linhas.
- **[Listando cores com BoxView](#listingcolors)** &ndash; Exiba todas as cores do sistema em um `ListView` .
- **[Jogando o jogo de vida por subclasse BoxView](#subclassing)** &ndash; Implemente um famoso automação de celular.
- **[Criando um relógio digital](#digitalclock)** &ndash; simular uma exibição de matriz de pontos.
- **[Criando um relógio analógico](#analogclock)** &ndash; transformar e animar `BoxView` elementos.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Definindo a cor e o tamanho do BoxView

Normalmente, você definirá as seguintes propriedades de `BoxView` :

- [`Color`](xref:Xamarin.Forms.BoxView.Color)para definir sua cor.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius)para definir seu raio de canto.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)para definir a largura do `BoxView` em unidades independentes do dispositivo.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)para definir a altura do `BoxView` .

A `Color` propriedade é do tipo `Color` ; a propriedade pode ser definida como qualquer `Color` valor, incluindo os 141 campos somente leitura estáticos de cores nomeadas que variam em ordem alfabética de `AliceBlue` para `YellowGreen` .

A `CornerRadius` propriedade é do tipo [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) ; a propriedade pode ser definida como um único `double` valor de raio de canto uniforme ou uma `CornerRadius` estrutura definida por quatro `double` valores que são aplicados à parte superior esquerda, superior direita, inferior esquerda e inferior direita do `BoxView` .

As `WidthRequest` `HeightRequest` Propriedades e só desempenham uma função se a `BoxView` não for *restringida* no layout. Esse é o caso quando o contêiner de layout precisa saber o tamanho do filho, por exemplo, quando o `BoxView` é um filho de uma célula de dimensionamento automático no `Grid` layout. Um `BoxView` também é irrestrito quando suas `HorizontalOptions` `VerticalOptions` Propriedades e são definidas como valores diferentes de `LayoutOptions.Fill` . Se o `BoxView` for irrestrito, mas as `WidthRequest` `HeightRequest` Propriedades e não estiverem definidas, a largura ou a altura serão definidas como valores padrão de 40 unidades ou cerca de 1/4 polegadas em dispositivos móveis.

As `WidthRequest` `HeightRequest` Propriedades e são ignoradas se o `BoxView` for *restrito* no layout; nesse caso, o contêiner de layout impõe seu próprio tamanho no `BoxView` .

Um `BoxView` pode ser restrito em uma dimensão e irrestrito no outro. Por exemplo, se o `BoxView` for um filho de um vertical `StackLayout` , a dimensão vertical do `BoxView` será irrestrita e sua dimensão horizontal geralmente será restrita. Mas há exceções para essa dimensão horizontal: se o `BoxView` tiver sua `HorizontalOptions` propriedade definida como algo diferente de `LayoutOptions.Fill` , a dimensão horizontal também será irrestrita. Também é possível que ele `StackLayout` tenha uma dimensão horizontal não restringida; nesse caso, o `BoxView` também será horizontalmente irrestrito.

O exemplo [**BasicBoxView**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview) exibe um quadrado de uma polegada não restringido `BoxView` no centro de sua página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Eis o resultado:

[![BoxView básico](boxview-images/basicboxview-small.png "BoxView básico")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Se as `VerticalOptions` `HorizontalOptions` Propriedades e forem removidas da `BoxView` marca ou forem definidas como `Fill` , o `BoxView` se tornará restrito pelo tamanho da página e se expandirá para preencher a página.

Um `BoxView` também pode ser um filho de um `AbsoluteLayout` . Nesse caso, o local e o tamanho do `BoxView` são definidos usando a `LayoutBounds` propriedade vinculável anexada. O `AbsoluteLayout` é abordado no artigo [**AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Você verá exemplos de todos esses casos nos programas de exemplo a seguir.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Renderizando decorações de texto

Você pode usar o `BoxView` para adicionar algumas decorações simples em suas páginas na forma de linhas horizontais e verticais. O exemplo [**TextDecoration**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration) demonstra isso. Todos os visuais do programa são definidos no arquivo **MainPage. XAML** , que contém vários `Label` `BoxView` elementos e no `StackLayout` mostrado aqui:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

Toda a marcação a seguir são filhos do `StackLayout` . Essa marcação consiste em vários tipos de elementos decorativos `BoxView` usados com o `Label` elemento:

[![Decoração de texto](boxview-images/textdecoration-small.png "Decoração de texto")](boxview-images/textdecoration-large.png#lightbox "Decoração de texto")

O cabeçalho de estilo superficial na parte superior da página é obtido com um `AbsoluteLayout` cujos filhos são quatro `BoxView` elementos e a `Label` , todos os quais são atribuídos locais e tamanhos específicos:

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

No arquivo XAML, o `AbsoluteLayout` é seguido por um `Label` com texto formatado que descreve o `AbsoluteLayout` .

Você pode sublinhar uma cadeia de caracteres de texto delimitando `Label` e `BoxView` em um `StackLayout` que tenha seu `HorizontalOptions` valor definido como algo diferente de `Fill` . A largura do `StackLayout` é, então, governada pela largura do `Label` , que, em seguida, impõe essa largura no `BoxView` . O `BoxView` é atribuído apenas a uma altura explícita:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

Você não pode usar essa técnica para sublinhar palavras individuais dentro de cadeias de caracteres de texto mais longas ou um parágrafo.

Também é possível usar um `BoxView` para se parecer com um elemento HTML `hr` (regra horizontal). Basta deixar a largura de `BoxView` ser determinada por seu contêiner pai, que nesse caso é o `StackLayout` :

```xaml
<BoxView HeightRequest="3" />
```

Por fim, você pode desenhar uma linha vertical em um lado de um parágrafo de texto delimitando o `BoxView` e o `Label` em uma horizontal `StackLayout` . Nesse caso, a altura do `BoxView` é igual à altura de `StackLayout` , que é governada pela altura do `Label` :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>Listando cores com BoxView

O `BoxView` é conveniente para exibir cores. Este programa usa um `ListView` para listar todos os campos somente leitura estáticos públicos da Xamarin.Forms `Color` estrutura:

[![Cores de ListView](boxview-images/listviewcolors-small.png "Cores de ListView")](boxview-images/listviewcolors-large.png#lightbox "Cores de ListView")

O programa [**ListViewColors**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors) inclui uma classe chamada `NamedColor` . O construtor estático usa a reflexão para acessar todos os campos da `Color` estrutura e criar um `NamedColor` objeto para cada um. Eles são armazenados na propriedade estática `All` :

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

Os visuais do programa são descritos no arquivo XAML. A `ItemsSource` propriedade de `ListView` é definida como a propriedade estática `NamedColor.All` , o que significa que o `ListView` exibe todos os `NamedColor` objetos individuais:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Os `NamedColor` objetos são formatados pelo `ViewCell` objeto que é definido como o modelo de dados do `ListView` . Esse modelo inclui um `BoxView` cuja `Color` propriedade está associada à `Color` Propriedade do `NamedColor` objeto.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Jogando o jogo de vida por subclasse BoxView

O jogo de vida é um automação de celular inventado por matemático John Conway e é popular nas páginas da *American americana* na 70. Uma boa introdução é fornecida pelo artigo da Wikipédia [Conway The Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

O Xamarin.Forms programa [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife) define uma classe chamada `LifeCell` derivada de `BoxView` . Essa classe encapsula a lógica de uma célula individual no jogo de vida:

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell`Adiciona mais três propriedades a `BoxView` : as `Col` `Row` Propriedades e armazenam a posição da célula dentro da grade e a `IsAlive` propriedade indica seu estado. A `IsAlive` propriedade também definirá a `Color` propriedade de `BoxView` como preto se a célula estiver ativa e será branca se a célula não estiver ativa.

`LifeCell`também instala um `TapGestureRecognizer` para permitir que o usuário alterne o estado das células tocando-as. A classe traduz o `Tapped` evento do reconhecedor de gestos em seu próprio `Tapped` evento.

O programa **GameOfLife** também inclui uma `LifeGrid` classe que encapsula grande parte da lógica do jogo e uma `MainPage` classe que manipula os visuais do programa. Isso inclui uma sobreposição que descreve as regras do jogo. Aqui está o programa em ação mostrando algumas centenas de `LifeCell` objetos na página:

[![Jogo de vida](boxview-images/gameoflife-small.png "Jogo de vida")](boxview-images/gameoflife-large.png#lightbox "Jogo de vida")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Criando um relógio digital

O programa [**DotMatrixClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock) cria 210 `BoxView` elementos para simular os pontos de uma exibição de matriz de pontos de 5 por 7 de maneira antiga. Você pode ler a hora no modo retrato ou paisagem, mas ela é maior em paisagem:

[![Relógio de matriz de pontos](boxview-images/dotmatrixclock-small.png "Relógio de matriz de pontos")](boxview-images/dotmatrixclock-large.png#lightbox "Relógio de matriz de pontos")

O arquivo XAML faz pouco mais do que instanciar o `AbsoluteLayout` usado para o relógio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

Tudo o mais ocorre no arquivo code-behind. A lógica de exibição de matriz pontilhada é bastante simplificada pela definição de várias matrizes que descrevem os pontos correspondentes a cada um dos 10 dígitos e dois-pontos:

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

Esses campos terminam com uma matriz tridimensional de `BoxView` elementos para armazenar os padrões de ponto para os seis dígitos.

O construtor cria todos os `BoxView` elementos para os dígitos e dois-pontos e também inicializa a `Color` propriedade dos `BoxView` elementos para os dois-pontos:

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

Este programa usa o posicionamento relativo e o recurso de dimensionamento do `AbsoluteLayout` . A largura e a altura de cada um `BoxView` são definidas como valores fracionários, especificamente 85% de 1 dividido pelo número de pontos horizontais e verticais. As posições também são definidas como valores fracionários.

Como todas as posições e tamanhos são relativos ao tamanho total do `AbsoluteLayout` , o manipulador da `SizeChanged` página só precisa definir um `HeightRequest` dos `AbsoluteLayout` :

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

A largura do `AbsoluteLayout` é definida automaticamente porque se estende até a largura total da página.

O código final na `MainPage` classe processa o retorno de chamada do temporizador e colore os pontos de cada dígito. A definição das matrizes multidimensionais no início do arquivo code-behind ajuda a tornar essa lógica a parte mais simples do programa:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```

<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>Criando um relógio analógico

Um relógio de matriz de pontos pode parecer ser um aplicativo óbvio `BoxView` , mas os `BoxView` elementos também são capazes de perceber um relógio analógico:

[![Relógio BoxView](boxview-images/boxviewclock-small.png "Relógio BoxView")](boxview-images/boxviewclock-large.png#lightbox "Relógio BoxView")

Todos os visuais no programa [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) são filhos de um `AbsoluteLayout` . Esses elementos são dimensionados usando a `LayoutBounds` Propriedade anexada e girados usando a `Rotation` propriedade.

Os três `BoxView` elementos para as mãos do relógio são instanciados no arquivo XAML, mas não posicionados ou dimensionados:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

O construtor do arquivo code-behind instancia os `BoxView` elementos 60 para as marcas de escala ao contrário da circunferência do relógio:

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

O dimensionamento e o posicionamento de todos os `BoxView` elementos ocorrem no `SizeChanged` manipulador para o `AbsoluteLayout` . Uma pequena estrutura interna à classe chamada `HandParams` descreve o tamanho de cada uma das três mãos em relação ao tamanho total do relógio:

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

O `SizeChanged` manipulador determina o centro e o raio do e, `AbsoluteLayout` em seguida, dimensiona e posiciona os `BoxView` elementos 60 usados como marcas de escala. O `for` loop é concluído definindo a `Rotation` propriedade de cada um desses `BoxView` elementos. No final do `SizeChanged` manipulador, o `LayoutHand` método é chamado para dimensionar e posicionar as três mãos do relógio:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

Os `LayoutHand` tamanhos e posições de método a cada mão para apontar diretamente para a posição 12:00. No final do método, a `AnchorY` propriedade é definida como uma posição correspondente ao centro do relógio. Isso indica o centro de rotação.

As mãos são giradas na função de retorno de chamada do temporizador:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

A segunda mão é tratada um pouco diferente: uma função de atenuação de animação é aplicada para fazer com que a movimentação pareça mecânica em vez de suave. Em cada tique, a segunda mão retorna um pouco e, em seguida, excede seu destino. Esse pouco de código adiciona muita coisa ao realismo da movimentação.

## <a name="conclusion"></a>Conclusão

O `BoxView` pode parecer simples a princípio, mas como você viu, pode ser bastante versátil e quase reproduzir visuais que normalmente são possíveis apenas com gráficos vetoriais. Para obter gráficos mais sofisticados, consulte [usando Xamarin.Forms o SkiaSharp no ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).

## <a name="related-links"></a>Links relacionados

- [BoxView básico (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [Decoração de texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [Cores de ListView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [Jogo de vida (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [Relógio de matriz de ponto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [Relógio BoxView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)
