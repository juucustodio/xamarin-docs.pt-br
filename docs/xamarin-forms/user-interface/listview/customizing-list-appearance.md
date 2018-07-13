---
title: Personalizando a aparência de ListView
description: Este artigo explica como personalizar ListViews em aplicativos xamarin. Forms usando os cabeçalhos, rodapés, grupos e as células de altura variável.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 1326a1326b4a88459e4e0a01ef590e770e3a88c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997342"
---
# <a name="customizing-listview-appearance"></a>Personalizando a aparência de ListView

`ListView` tem opções para controlar a apresentação da lista global, além de subjacente `ViewCell`s. As opções incluem:

- [**Agrupando** ](#Grouping) &ndash; agrupar itens na ListView para facilitar a navegação e melhor organização.
- [**Cabeçalhos e rodapés** ](#Headers_and_Footers) &ndash; exibir informações no início e término do modo de exibição que rola com os outros itens.
- [**Separadores de linha** ](#Row_Separators) &ndash; Mostrar ou ocultar as linhas de separador entre itens.
- [**Linhas de altura variável** ](#Row_Heights) &ndash; por padrão, todas as linhas têm a mesma altura, mas isso pode ser alterado para permitir a linhas com alturas diferentes a serem exibidos.

<a name="Grouping" />

## <a name="grouping"></a>Agrupamento
Muitas vezes, grandes conjuntos de dados podem se tornar complicados quando apresentadas em uma lista de rolagem contínua. Habilitação de agrupamento pode melhorar a experiência do usuário nesses casos, organizar melhor o conteúdo e ativar os controles específicos da plataforma que facilitam a navegação de dados.

Quando o agrupamento é ativado para um `ListView`, uma linha de cabeçalho é adicionada para cada grupo.

Para habilitar o agrupamento:

- Crie uma lista de listas (uma lista de grupos, cada grupo que está sendo uma lista de elementos).
- Defina as `ListView`do `ItemsSource` à lista.
- Definir `IsGroupingEnabled` como true.
- Definir [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) para associar a propriedade dos grupos que está sendo usada como o título do grupo.
- [Opcional] Definir [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) para associar à propriedade que está sendo usada como o nome curto para o grupo dos grupos. O nome curto é usado para as listas de salto (coluna direita no iOS).

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

No código acima, `All` é a lista será dado ao nosso ListView como a origem da associação. `Title` e `ShortName` são as propriedades que serão usadas para títulos de grupo.

Nesse estágio, `All` é uma lista vazia. Adicione um construtor estático para que a lista será preenchida no início do programa:

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
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
        }
        All = Groups; //set the publicly accessible list
}
```

No código acima também podemos chamar `Add` nos elementos de `groups`, que são instâncias do tipo `PageTypeGroup`. Isso é possível porque `PageTypeGroup` herda de `List<PageModel>`. Este é um exemplo da lista de padrão de listas observado acima.

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

O resultado é o seguinte:

![](customizing-list-appearance-images/grouping-depth.png "Exemplo de agrupamento de ListView")

Observe que temos:

- Definir `GroupShortNameBinding` para o `ShortName` propriedade definida em nossa classe de grupo
- Definir `GroupDisplayBinding` para o `Title` propriedade definida em nossa classe de grupo
- Definir `IsGroupingEnabled` como true
- Alterado o `ListView`do `ItemsSource` à lista agrupada

### <a name="customizing-grouping"></a>Personalizar agrupamento

Se o agrupamento tiver sido habilitado na lista, o cabeçalho de grupo também pode ser personalizado.

Assim como o `ListView` tem um `ItemTemplate` para definir como as linhas são exibidas, `ListView` tem um `GroupHeaderTemplate`.

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

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>Cabeçalhos e rodapés
É possível para um ListView apresentar um cabeçalho e rodapé que rolam com os elementos da lista. O cabeçalho e rodapé podem ser cadeias de caracteres de texto ou um layout mais complicado. Observe que isso é separado do [seção grupos](#Grouping).

Você pode definir as `Header` e/ou `Footer` para uma cadeia de caracteres simple valor, ou você pode defini-las para um layout mais complexo.
Também há `HeaderTemplate` e `FooterTemplate` propriedades que permitem criam layouts mais complexos para o cabeçalho e rodapé que suportam associação de dados.

Para criar um cabeçalho/rodapé simple, basta defina as propriedades de cabeçalho ou rodapé para o texto que você deseja exibir. No código:

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

No XAML:

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView com cabeçalho e rodapé")

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

![](customizing-list-appearance-images/header-custom.png "ListView com personalizado de cabeçalho e rodapé")

<a name="Row_Separators" />

## <a name="row-separators"></a>Separadores de linha
As linhas do separador são exibidas entre `ListView` elementos por padrão no iOS e Android. Se você desejar ocultar as linhas de separador no iOS e Android, defina o `SeparatorVisibility` propriedade em sua ListView. As opções para `SeparatorVisibility` são:

* **Padrão** -mostra uma linha separadora no iOS e Android.
* **Nenhum** -oculta o separador em todas as plataformas.

Visibilidade do padrão:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView com separadores de linha padrão")

Nenhum:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView sem separadores de linha")

Você também pode definir a cor da linha do separador via o `SeparatorColor` propriedade:

C#:

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView com separadores de linha verde")

> [!NOTE]
> Definindo cada uma dessas propriedades no Android, após o carregamento de `ListView` resulta em uma penalidade de desempenho grande.

<a name="Row_Heights" />

## <a name="row-heights"></a>As alturas das linhas
Por padrão, todas as linhas em um ListView têm a mesma altura. ListView tem duas propriedades que podem ser usadas para alterar esse comportamento:

- `HasUnevenRows` &ndash; `true`/`false` valor, as linhas têm diferentes alturas se definido como `true`. Assume o padrão de `false`.
- `RowHeight` &ndash; Define a altura de cada a linha quando `HasUnevenRows` é `false`.

Você pode definir a altura de todas as linhas, definindo o `RowHeight` propriedade no `ListView`.

### <a name="custom-fixed-row-height"></a>Altura de linha fixa personalizada

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView com altura de linha fixa")


### <a name="uneven-rows"></a>Linhas irregulares

Se você quiser linhas individuais ter alturas diferentes, você pode definir as `HasUnevenRows` propriedade para `true`.
Observe que as alturas das linhas não precisam ser definido manualmente uma vez `HasUnevenRows` foi definida como `true`, pois as alturas serão calculadas automaticamente pelo xamarin. Forms.


C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView com linhas irregulares")

### <a name="runtime-resizing-of-rows"></a>Redimensionamento de tempo de execução de linhas

Individuais `ListView` linhas podem ser redimensionadas por meio de programação em tempo de execução, desde que o `HasUnevenRows` estiver definida como `true`. O [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método atualiza o tamanho da célula, mesmo quando não estiver visível no momento, conforme demonstrado no exemplo de código a seguir:

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

O `OnImageTapped` manipulador de eventos é executado em resposta a uma [ `Image` ](xref:Xamarin.Forms.Image) em uma célula que está sendo tocado e aumenta o tamanho do `Image` exibido na célula para que ela é exibida com facilidade.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView com redimensionamento da linha do tempo de execução")

Observe que há uma grande possibilidade de degradação do desempenho se esse recurso está sendo usado em excesso.



## <a name="related-links"></a>Links relacionados

- [Agrupamento (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Exibição de renderizador personalizado (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [Redimensionamento de linhas dinâmicas (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/DynamicUnevenListCells/)
- [Notas de versão 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [Notas de versão 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
