---
title: Aparência de ListView
description: Este artigo explica como personalizar ListViews em aplicativos xamarin. Forms usando os cabeçalhos, rodapés, grupos e as células de altura variável.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 90b0e0f3802ce766decb802c9406d72b5966360e
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304005"
---
# <a name="listview-appearance"></a>Aparência de ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

O [`ListView`](xref:Xamarin.Forms.ListView) Xamarin. Forms permite que você personalize a apresentação da lista, além das instâncias de [`ViewCell`](xref:Xamarin.Forms.ViewCell) para cada linha da lista.

## <a name="grouping"></a>Agrupamento

Grandes conjuntos de dados podem se tornar difíceis quando apresentados em uma lista de rolagem contínua. Habilitação de agrupamento pode melhorar a experiência do usuário nesses casos, organizar melhor o conteúdo e ativar os controles específicos da plataforma que facilitam a navegação de dados.

Quando o agrupamento é ativado para um `ListView`, uma linha de cabeçalho é adicionada a cada grupo.

Para habilitar o agrupamento:

- Crie uma lista de listas (uma lista de grupos, cada grupo que está sendo uma lista de elementos).
- Defina o `ItemsSource` do `ListView`para essa lista.
- Defina `IsGroupingEnabled` como true.
- Defina [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) para associar à propriedade dos grupos que estão sendo usados como o título do grupo.
- Adicional Defina [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) para associar à propriedade dos grupos que estão sendo usados como o nome curto do grupo. O nome curto é usado para as listas de salto (coluna direita no iOS).

Comece criando uma classe para os grupos:

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

No código acima, `All` é a lista que será dada ao nosso ListView como a origem da associação. `Title` e `ShortName` são as propriedades que serão usadas para títulos de grupo.

Neste estágio, `All` é uma lista vazia. Adicione um construtor estático para que a lista será preenchida no início do programa:

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alpha", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        };
        All = Groups; //set the publicly accessible list
}
```

No código acima, também podemos chamar `Add` em elementos de `Groups`, que são instâncias do tipo `PageTypeGroup`. Esse método é possível porque `PageTypeGroup` herda de `List<PageModel>`.

Aqui está o XAML para exibir a lista agrupada:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
                   GroupDisplayBinding="{Binding Title}"
                   GroupShortNameBinding="{Binding ShortName}"
                   IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Esse XAML executa as seguintes ações:

- Definir `GroupShortNameBinding` para a propriedade `ShortName` definida em nossa classe de grupo
- Definir `GroupDisplayBinding` para a propriedade `Title` definida em nossa classe de grupo
- Definir `IsGroupingEnabled` como true
- Alterou o `ItemsSource` do `ListView`para a lista agrupada

A captura de tela a seguir mostra a interface do usuário resultante:

![](customizing-list-appearance-images/grouping-depth.png "ListView Grouping Example")

### <a name="customizing-grouping"></a>Personalizando o agrupamento

Se o agrupamento tiver sido habilitado na lista, o cabeçalho de grupo também pode ser personalizado.

Semelhante a como o `ListView` tem um `ItemTemplate` para definir como as linhas são exibidas, `ListView` tem um `GroupHeaderTemplate`.

Um exemplo de como personalizar o cabeçalho de grupo em XAML é mostrado aqui:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
                  GroupDisplayBinding="{Binding Title}"
                  GroupShortNameBinding="{Binding ShortName}"
                  IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding ShortName}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

## <a name="headers-and-footers"></a>Cabeçalhos e rodapés

É possível para um ListView apresentar um cabeçalho e rodapé que rolam com os elementos da lista. O cabeçalho e rodapé podem ser cadeias de caracteres de texto ou um layout mais complicado. Esse comportamento é separado dos [grupos de seções](#grouping).

Você pode definir o `Header` e/ou `Footer` para um valor `string` ou pode defini-los como um layout mais complexo. Também há `HeaderTemplate` e `FooterTemplate` propriedades que permitem criar layouts mais complexos para o cabeçalho e o rodapé que dão suporte à vinculação de dados.

Para criar um cabeçalho/rodapé básico, basta definir as propriedades de cabeçalho ou rodapé como o texto que você deseja exibir. No código:

```csharp
ListView HeaderList = new ListView()
{
    Header = "Header",
    Footer = "Footer"
};
```

No XAML:

```xaml
<ListView x:Name="HeaderList" 
          Header="Header"
          Footer="Footer">
    ...
</ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView with Header and Footer")

Para criar um cabeçalho personalizado e um rodapé, defina os modos de exibição do cabeçalho e rodapé:

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
               TextColor="Olive"
               BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
               TextColor="Gray"
               BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "ListView with Customized Header and Footer")

## <a name="scrollbar-visibility"></a>Visibilidade da barra de rolagem

A classe [`ListView`](xref:Xamarin.Forms.ListView) tem `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility` Propriedades, que Obtém ou define um valor de [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa quando a barra de rolagem horizontal ou vertical é visível. Ambas as propriedades podem ser definidas com os seguintes valores:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indica o comportamento padrão da barra de rolagem para a plataforma e é o valor padrão para as propriedades `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indica que as barras de rolagem estarão visíveis, mesmo quando o conteúdo couber na exibição.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indica que as barras de rolagem não estarão visíveis, mesmo que o conteúdo não caiba na exibição.

## <a name="row-separators"></a>Separadores de linha

As linhas separadoras são exibidas entre os elementos de `ListView` por padrão no iOS e no Android. Se você preferir ocultar as linhas separadoras no iOS e no Android, defina a propriedade `SeparatorVisibility` em ListView. As opções para `SeparatorVisibility` são:

- **Padrão** -mostra uma linha separadora no Ios e Android.
- **Nenhum** – oculta o separador em todas as plataformas.

Visibilidade do padrão:

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView with Default Row Separators")

Nenhum:

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView without Row Separators")

Você também pode definir a cor da linha do separador por meio da propriedade `SeparatorColor`:

C#:

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView with Green Row Separators")

> [!NOTE]
> Definir qualquer uma dessas propriedades no Android depois de carregar a `ListView` incorre em uma grande penalidade de desempenho.

## <a name="row-height"></a>Altura da linha

Por padrão, todas as linhas em um ListView têm a mesma altura. ListView tem duas propriedades que podem ser usadas para alterar esse comportamento:

- `HasUnevenRows` &ndash; `true`/valor de `false`, as linhas têm alturas variáveis, se definidas como `true`. Assume o padrão de `false`.
- `RowHeight` &ndash; define a altura de cada linha quando `HasUnevenRows` é `false`.

Você pode definir a altura de todas as linhas definindo a propriedade `RowHeight` na `ListView`.

### <a name="custom-fixed-row-height"></a>Altura de linha fixa personalizada

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView with Fixed Row Height")

### <a name="uneven-rows"></a>Linhas desiguais

Se desejar que as linhas individuais tenham alturas diferentes, você poderá definir a propriedade `HasUnevenRows` como `true`. As alturas de linha não precisam ser definidas manualmente quando `HasUnevenRows` foi definido como `true`, porque as alturas serão calculadas automaticamente pelo Xamarin. Forms.

C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView with Uneven Rows")

### <a name="resize-rows-at-runtime"></a>Redimensionar linhas em tempo de execução

Linhas `ListView` individuais podem ser programaticamente redimensionadas em tempo de execução, desde que a propriedade `HasUnevenRows` esteja definida como `true`. O método [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) atualiza o tamanho de uma célula, mesmo quando ela não está visível no momento, conforme demonstrado no exemplo de código a seguir:

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

O manipulador de eventos `OnImageTapped` é executado em resposta a um [`Image`](xref:Xamarin.Forms.Image) em uma célula que está sendo tocada e aumenta o tamanho do `Image` exibido na célula para que ele seja facilmente exibido.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView with Runtime Row Resizing")

> [!WARNING]
> O uso excessivo do redimensionamento de linha de tempo de execução pode causar degradação de desempenho.

## <a name="related-links"></a>Links relacionados

- [Agrupamento (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Exibição do renderizador personalizado (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Redimensionamento dinâmico de linhas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [notas de versão de 1,4](https://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [notas de versão de 1,3](https://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
