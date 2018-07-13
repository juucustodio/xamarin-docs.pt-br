---
title: Introdução aos modelos de dados do xamarin. Forms
description: Modelos de dados do xamarin. Forms fornecem a capacidade de definir a apresentação dos dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 129ce7a04b93bfb3cb1b9a1639aee61cd56d09d5
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998903"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introdução aos modelos de dados do xamarin. Forms

_Modelos de dados do xamarin. Forms fornecem a capacidade de definir a apresentação dos dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários._

Considere uma [ `ListView` ](xref:Xamarin.Forms.ListView) que exibe uma coleção de `Person` objetos. O exemplo de código a seguir mostra a definição do `Person` classe:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

O `Person` classe define `Name`, `Age`, e `Location` as propriedades, que podem ser definidas quando um `Person` objeto é criado. O [ `ListView` ](xref:Xamarin.Forms.ListView) é usado para exibir a coleção de `Person` objetos, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Itens são adicionados à [ `ListView` ](xref:Xamarin.Forms.ListView) no XAML Inicializando o [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade de uma matriz de `Person` instâncias.

> [!NOTE]
> Observe que o `x:Array` elemento requer um `Type` atributo que indica o tipo dos itens na matriz.

A página c# equivalente é mostrada no exemplo de código a seguir, que inicializa o [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade como um `List` de `Person` instâncias:

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

O [ `ListView` ](xref:Xamarin.Forms.ListView) chamadas `ToString` ao exibir os objetos na coleção. Porque não há nenhuma `Person.ToString` substituir, `ToString` retorna o nome do tipo de cada objeto, conforme mostrado nas capturas de tela seguir:

![](introduction-images/no-data-template.png "ListView sem um modelo de dados")

O `Person` objeto pode substituir o `ToString` método para exibir dados significativos, como mostrado no exemplo de código a seguir:

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

Isso resulta nos [ `ListView` ](xref:Xamarin.Forms.ListView) exibindo o `Person.Name` valor da propriedade para cada objeto na coleção, conforme mostrado nas capturas de tela seguir:

![](introduction-images/override-tostring.png "ListView com um modelo de dados")

O `Person.ToString` substituição poderia retornar uma cadeia de caracteres formatada que consiste o `Name`, `Age`, e `Location` propriedades. No entanto, essa abordagem oferece apenas um controle limitado sobre a aparência de cada item de dados. Para obter mais flexibilidade, um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) pode ser criado que define a aparência dos dados.

## <a name="creating-a-datatemplate"></a>Criando um DataTemplate

Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) é usado para especificar a aparência dos dados e geralmente usa a associação de dados para exibir dados. Seu cenário de uso comum é ao exibir dados de uma coleção de objetos em um [ `ListView` ](xref:Xamarin.Forms.ListView). Por exemplo, quando um `ListView` está associado a uma coleção de `Person` objetos, o `ListView.ItemTemplate` propriedade será definida como um `DataTemplate` que define a aparência de cada `Person` objeto o `ListView`. O `DataTemplate` conterá os elementos que mapeiam para os valores de propriedade de cada `Person` objeto. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) pode ser usado como um valor para as seguintes propriedades:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), que são herdadas pelos [ `ListView` ](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), que são herdadas pelos [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), e [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage).

> [!NOTE]
> Observe que, embora o [ `TableView` ](xref:Xamarin.Forms.TableView) faz uso de [ `Cell` ](xref:Xamarin.Forms.Cell) objetos, ele não usa um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Isso ocorre porque as associações de dados sempre são definidas diretamente no `Cell` objetos.

Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que é inserido como um filho direto das propriedades listadas acima é conhecido como um *modelo embutido*. Como alternativa, um `DataTemplate` pode ser definido como um recurso de nível de controle, o nível de página ou o nível de aplicativo. Escolhendo onde definir um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) impactos em que ele pode ser usado:

- Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definido para o controle nível só pode ser aplicado ao controle.
- Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definidos na página do nível pode ser aplicado a vários controles válidos na página.
- Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definidos no nível do aplicativo pode ser aplicado a válido controles em todo o aplicativo.

Modelos de dados inferiores na hierarquia de exibição têm precedência sobre aquelas definidas para cima superior quando eles compartilham `x:Key` atributos. Por exemplo, um modelo de dados de nível de aplicativo será substituído por um modelo de dados de nível de página e um modelo de dados de nível de página será substituído por um modelo de dados de nível de controle ou um modelo de dados embutido.


## <a name="related-links"></a>Links relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
