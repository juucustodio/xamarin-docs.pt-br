---
title: Grupo Xamarin.Forms CollectionView
description: O CollectionView pode exibir dados corretamente agrupados definindo sua propriedade IsGrouped como true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8360123b01f36bde084b4dc315109e6bdaef2207
ms.sourcegitcommit: 99aa05bd9b5e3f66d134066b860f41b54fa2d850
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103276"
---
# <a name="xamarinforms-collectionview-grouping"></a>Grupo Xamarin.Forms CollectionView

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

Grandes conjuntos de dados podem muitas vezes tornar-se desorganizados quando apresentados em uma lista de rolagem contínua. Nesse cenário, organizar os dados em grupos pode melhorar a experiência do usuário, facilitando a navegação dos dados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)suporta a exibição de dados agrupados e define as seguintes propriedades que controlam como elas serão apresentadas:

- `IsGrouped`, do `bool`tipo, indica se os dados subjacentes devem ser exibidos em grupos. O valor padrão dessa propriedade é `false`.
- `GroupHeaderTemplate`, do [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)tipo , o modelo a ser usado para o cabeçalho de cada grupo.
- `GroupFooterTemplate`, do [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)tipo , o modelo a ser usado para o rodapé de cada grupo.

Essas propriedades são [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) apoiadas por objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

As capturas de [`CollectionView`](xref:Xamarin.Forms.CollectionView) tela a seguir mostram uma exibição de dados agrupados:

[![Captura de tela de um grupo de dados em um CollectionView, no iOS e Android](grouping-images/grouped-data.png "CollectionVer com dados agrupados")](grouping-images/grouped-data-large.png#lightbox "CollectionVer com dados agrupados")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="group-data"></a>Dados do grupo

Os dados devem ser agrupados antes de serem exibidos. Isso pode ser feito criando uma lista de grupos, onde cada grupo é uma lista de itens. A lista de grupos `IEnumerable<T>` deve `T` ser uma coleção, onde define dois dados:

- Um nome de grupo.
- Uma `IEnumerable` coleção que define os itens pertencentes ao grupo.

O processo de agrupamento de dados, portanto, é:

- Crie um tipo que modela um único item.
- Crie um tipo que modele um único grupo de itens.
- Crie `IEnumerable<T>` uma coleção, onde `T` está o tipo que modela um único grupo de itens. Essa coleção é, portanto, uma coleção de grupos, que armazena os dados agrupados.
- Adicione dados `IEnumerable<T>` à coleção.

### <a name="example"></a>Exemplo

Ao agrupar dados, o primeiro passo é criar um tipo que modele um único item. O exemplo a `Animal` seguir mostra a classe a partir da aplicação da amostra:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

A `Animal` classe modela um único item. Um tipo que modela um grupo de itens pode então ser criado. O exemplo a `AnimalGroup` seguir mostra a classe a partir da aplicação da amostra:

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

A `AnimalGroup` classe herda `List<T>` da classe `Name` e adiciona uma propriedade que representa o nome do grupo.

Uma `IEnumerable<T>` coleção de grupos pode então ser criada:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Este código define uma `Animals`coleção nomeada , onde cada `AnimalGroup` item da coleção é um objeto. Cada `AnimalGroup` objeto compreende um nome `List<Animal>` e uma `Animal` coleção que define os objetos do grupo.

Os dados agrupados podem `Animals` então ser adicionados à coleção:

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
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Este código cria dois `Animals` grupos na coleção. O `AnimalGroup` primeiro `Bears`é nomeado `List<Animal>` , e contém uma coleção de detalhes de urso. O `AnimalGroup` segundo `Monkeys`é nomeado `List<Animal>` , e contém uma coleção de detalhes macaco.

## <a name="display-grouped-data"></a>Exibir dados agrupados

[`CollectionView`](xref:Xamarin.Forms.CollectionView)exibirá dados agrupados, desde que os dados foram agrupados `IsGrouped` corretamente, definindo a propriedade como `true`:

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

O aparecimento de cada [`CollectionView`](xref:Xamarin.Forms.CollectionView) item no [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) é definido [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)definindo a propriedade como a . Para obter mais informações, consulte [Definir aparência do item](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance).

> [!NOTE]
> Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá o nome do grupo no cabeçalho e rodapé do grupo. Esse comportamento pode ser alterado personalizando o cabeçalho de grupo e o rodapé de grupo.

## <a name="customize-the-group-header"></a>Personalize o cabeçalho do grupo

A aparência de cada cabeçalho de `CollectionView.GroupHeaderTemplate` grupo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)pode ser personalizada definindo a propriedade como:

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

Neste exemplo, cada cabeçalho [`Label`](xref:Xamarin.Forms.Label) de grupo é definido como um que exibe o nome do grupo e que tem outras propriedades de aparência definidas. As capturas de tela a seguir mostram o cabeçalho de grupo personalizado:

[![Captura de tela de um cabeçalho de grupo personalizado em um CollectionView, no iOS e Android](grouping-images/customized-header.png "CollectionVer com cabeçalho de grupo personalizado")](grouping-images/customized-header-large.png#lightbox "CollectionVer com cabeçalho de grupo personalizado")

## <a name="customize-the-group-footer"></a>Personalize o rodapé de grupo

A aparência de cada rodapé de grupo `CollectionView.GroupFooterTemplate` pode [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)ser personalizada definindo a propriedade como:

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

Neste exemplo, cada rodapé de [`Label`](xref:Xamarin.Forms.Label) grupo é definido como um que exibe o número de itens no grupo. As capturas de tela a seguir mostram o rodapé de grupo personalizado:

[![Captura de tela de um rodapé de grupo personalizado em um CollectionView, no iOS e Android](grouping-images/customized-footer.png "ColeçãoVer com rodapé de grupo personalizado")](grouping-images/customized-footer-large.png#lightbox "ColeçãoVer com rodapé de grupo personalizado")

## <a name="empty-groups"></a>Grupos vazios

Quando [`CollectionView`](xref:Xamarin.Forms.CollectionView) um exibe dados agrupados, ele exibirá todos os grupos que estiverem vazios. Esses grupos serão exibidos com um cabeçalho de grupo e rodapé, indicando que o grupo está vazio. As capturas de tela a seguir mostram um grupo vazio:

[![Captura de tela de um grupo vazio em um CollectionView, no iOS e Android](grouping-images/empty-group.png "ColeçãoVer com um grupo vazio")](grouping-images/empty-group-large.png#lightbox "ColeçãoVer com um grupo vazio")

> [!NOTE]
> No iOS 10 e inferior, cabeçalhos de grupo e rodapés `CollectionView`para grupos vazios podem ser exibidos na parte superior do .

## <a name="group-without-templates"></a>Grupo sem modelos

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode exibir dados corretamente agrupados sem definir a [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como: [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

Nesse cenário, dados significativos podem ser `ToString` exibidos substituindo o método no tipo que modela um único item e o tipo que modela um único grupo de itens.

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
