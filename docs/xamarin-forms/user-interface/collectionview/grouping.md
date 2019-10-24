---
title: Agrupamento de CollectionView do Xamarin. Forms
description: CollectionView pode exibir dados agrupados corretamente definindo sua propriedade isgroupd como true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 3a0fe7159e6af24d58e49dea4166d012605c9985
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749803"
---
# <a name="xamarinforms-collectionview-grouping"></a>Agrupamento de CollectionView do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

Conjuntos de dados grandes geralmente podem se tornar difíceis quando apresentados em uma lista de rolagem contínua. Nesse cenário, organizar os dados em grupos pode melhorar a experiência do usuário, facilitando a navegação dos dados.

o [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte à exibição de dados agrupados e define as seguintes propriedades que controlam como ele será apresentado:

- `IsGrouped`, do tipo `bool`, indica se os dados subjacentes devem ser exibidos em grupos. O valor padrão dessa propriedade é `false`.
- `GroupHeaderTemplate`, do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), o modelo a ser usado para o cabeçalho de cada grupo.
- `GroupFooterTemplate`, do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), o modelo a ser usado para o rodapé de cada grupo.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

As capturas de tela a seguir mostram uma [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibir dados agrupados:

[![Captura de tela de dados agrupados em um CollectionView, no iOS e no Android](grouping-images/grouped-data.png "CollectionView com dados agrupados")](grouping-images/grouped-data-large.png#lightbox "CollectionView com dados agrupados")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="group-data"></a>Agrupar dados

Os dados devem ser agrupados para que possam ser exibidos. Isso pode ser feito criando uma lista de grupos, onde cada grupo é uma lista de itens. A lista de grupos deve ser uma coleção de `IEnumerable<T>`, onde `T` define duas partes de dados:

- Um nome de grupo.
- Uma coleção de `IEnumerable` que define os itens que pertencem ao grupo.

O processo de agrupamento de dados, portanto, é:

- Crie um tipo que modela um único item.
- Crie um tipo que modela um único grupo de itens.
- Crie uma coleção de `IEnumerable<T>`, em que `T` é o tipo que modela um único grupo de itens. Essa coleção é, portanto, uma coleção de grupos, que armazena os dados agrupados.
- Adicione dados à coleção de `IEnumerable<T>`.

### <a name="example"></a>Exemplo

Ao agrupar dados, a primeira etapa é criar um tipo que modela um único item. O exemplo a seguir mostra a classe `Animal` do aplicativo de exemplo:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

A classe `Animal` modela um único item. Um tipo que modela um grupo de itens pode ser criado. O exemplo a seguir mostra a classe `AnimalGroup` do aplicativo de exemplo:

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

A classe `AnimalGroup` herda da classe `List<T>` e adiciona uma propriedade `Name` que representa o nome do grupo.

Uma `IEnumerable<T>` coleção de grupos pode ser criada:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Esse código define uma coleção chamada `Animals`, onde cada item na coleção é um objeto `AnimalGroup`. Cada objeto de `AnimalGroup` consiste em um nome e uma coleção de `List<Animal>` que define os objetos de `Animal` no grupo.

Os dados agrupados podem ser adicionados à coleção de `Animals`:

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Esse código cria dois grupos na coleção de `Animals`. O primeiro `AnimalGroup` é nomeado `Bears` e contém uma coleção de `List<Animal>` de detalhes de Bear. A segunda `AnimalGroup` é chamada de `Monkeys` e contém uma coleção `List<Animal>` de detalhes de macaco.

## <a name="display-grouped-data"></a>Exibir dados agrupados

[`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá os dados agrupados, desde que os dados tenham sido agrupados corretamente, definindo a propriedade `IsGrouped` como `true`:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
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
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

A aparência de cada item na [`CollectionView`](xref:Xamarin.Forms.CollectionView) é definida pela definição da propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obter mais informações, consulte [definir a aparência do item](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance).

> [!NOTE]
> Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá o nome do grupo no cabeçalho e no rodapé do grupo. Esse comportamento pode ser alterado Personalizando o cabeçalho do grupo e o rodapé do grupo.

## <a name="customize-the-group-header"></a>Personalizar o cabeçalho do grupo

A aparência de cada cabeçalho de grupo pode ser personalizada definindo a propriedade `CollectionView.GroupHeaderTemplate` como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

Neste exemplo, cada cabeçalho de grupo é definido como um [`Label`](xref:Xamarin.Forms.Label) que exibe o nome do grupo e que tem outras propriedades de aparência definidas. As capturas de tela a seguir mostram o cabeçalho de grupo personalizado:

[![Captura de tela de um cabeçalho de grupo personalizado em um CollectionView, no iOS e no Android](grouping-images/customized-header.png "CollectionView com cabeçalho de grupo personalizado")](grouping-images/customized-header-large.png#lightbox "CollectionView com cabeçalho de grupo personalizado")

## <a name="customize-the-group-footer"></a>Personalizar o rodapé do grupo

A aparência de cada rodapé de grupo pode ser personalizada definindo a propriedade `CollectionView.GroupFooterTemplate` como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

Neste exemplo, cada rodapé de grupo é definido como um [`Label`](xref:Xamarin.Forms.Label) que exibe o número de itens no grupo. As capturas de tela a seguir mostram o rodapé do grupo personalizado:

[![Captura de tela de um rodapé de grupo personalizado em um CollectionView, no iOS e no Android](grouping-images/customized-footer.png "CollectionView com rodapé de grupo personalizado")](grouping-images/customized-footer-large.png#lightbox "CollectionView com rodapé de grupo personalizado")

## <a name="empty-groups"></a>Grupos vazios

Quando um [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibe dados agrupados, ele exibirá todos os grupos vazios. Esses grupos serão exibidos com um cabeçalho e um rodapé de grupo, indicando que o grupo está vazio. As capturas de tela a seguir mostram um grupo vazio:

[![Captura de tela de um grupo vazio em um CollectionView, no iOS e no Android](grouping-images/empty-group.png "CollectionView com um grupo vazio")](grouping-images/empty-group-large.png#lightbox "CollectionView com um grupo vazio")

> [!NOTE]
> No iOS 10 e inferior, os cabeçalhos e rodapés de grupo de grupos vazios podem ser exibidos na parte superior da `CollectionView`.

## <a name="group-without-templates"></a>Grupo sem modelos

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir dados agrupados corretamente sem definir a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

Nesse cenário, dados significativos podem ser exibidos substituindo o método `ToString` no tipo que modela um único item e o tipo que modela um único grupo de itens.

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelos de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
