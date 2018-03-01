---
title: "Introdução"
description: "Modelos de dados do xamarin. Forms fornecem a capacidade de definir a apresentação dos dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: e7c1a8b233538b7ad40a18e44747bef672210516
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction"></a>Introdução

_Modelos de dados do xamarin. Forms fornecem a capacidade de definir a apresentação dos dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários._

Considere um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que exibe uma coleção de `Person` objetos. O exemplo de código a seguir mostra a definição do `Person` classe:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

O `Person` classe define `Name`, `Age`, e `Location` propriedades, que podem ser definidas quando um `Person` objeto é criado. O [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é usado para exibir a coleção de `Person` objetos, conforme mostrado no exemplo de código XAML a seguir:

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

Itens são adicionados para o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) em XAML Inicializando o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propriedade de uma matriz de `Person` instâncias.

> [!NOTE]
> Observe que o `x:Array` elemento requer uma `Type` atributo que indica o tipo dos itens na matriz.

A página c# equivalente é mostrada no exemplo de código a seguir, que inicializa o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propriedade para um `List` de `Person` instâncias:

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

O [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) chamadas `ToString` ao exibir os objetos na coleção. Porque não há nenhum `Person.ToString` substituir, `ToString` retorna o nome do tipo de cada objeto, como mostrado nas capturas de tela seguir:

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

Isso resulta na [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) exibindo o `Person.Name` valor de propriedade para cada objeto na coleção, conforme mostrado nas capturas de tela seguir:

![](introduction-images/override-tostring.png "ListView com um modelo de dados")

O `Person.ToString` substituição pode retornar uma cadeia de caracteres formatada que consiste o `Name`, `Age`, e `Location` propriedades. No entanto, essa abordagem oferece apenas um controle limitado sobre a aparência de cada item de dados. Para obter mais flexibilidade, um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) podem ser criados que define a aparência dos dados.

## <a name="creating-a-datatemplate"></a>Criando um DataTemplate

Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) é usado para especificar a aparência dos dados e geralmente usa associação de dados para exibir dados. O cenário de uso comum é ao exibir dados de uma coleção de objetos em um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Por exemplo, quando um `ListView` está associado a uma coleção de `Person` objetos, o `ListView.ItemTemplate` propriedade será definida um `DataTemplate` que define a aparência de cada `Person` objeto o `ListView`. O `DataTemplate` conterá os elementos que mapeiam para os valores de propriedade de cada `Person` objeto. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) pode ser usado como um valor para as seguintes propriedades:

- [`ListView.HeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HeaderTemplate/)
- [`ListView.FooterTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.FooterTemplate/)
- [`ListView.GroupHeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)
- [`ItemsView.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/), que é herdado por [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/).
- [`MultiPage.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/), que é herdado por [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), e [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/).

> [!NOTE]
> Observe que embora o [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) usa de [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) objetos, ela não usa um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Isso ocorre porque as associações de dados sempre são definidas diretamente no `Cell` objetos.

Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que é colocada como um filho direto das propriedades listadas acima é conhecido como um *embutido modelo*. Como alternativa, um `DataTemplate` pode ser definido como um recurso de nível de controle, em nível de página ou nível de aplicativo. Escolhendo onde definir um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) impactos onde ele pode ser usado:

- Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definido no controle nível só pode ser aplicado ao controle.
- Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definidos na página de nível pode ser aplicado a vários controles válidos na página.
- Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definidos no nível do aplicativo podem ser aplicados a válido controles em todo o aplicativo.

Inferior na hierarquia do modo de exibição de modelos de dados têm precedência sobre aquelas definidas superior para cima quando eles compartilham `x:Key` atributos. Por exemplo, um modelo de dados de nível de aplicativo será substituído por um modelo de dados de nível de página, e um modelo de dados de nível de página será substituído por um modelo de dados de nível de controle ou um modelo de dados embutidos.


## <a name="related-links"></a>Links relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
