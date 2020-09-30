---
title: Introdução aos Xamarin.Forms modelos de dados
description: Xamarin.Forms os modelos de dados fornecem a capacidade de definir a apresentação de dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28dbbe6411983e1fd7986413ceac69a5543c4bb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562568"
---
# <a name="introduction-to-no-locxamarinforms-data-templates"></a>Introdução aos Xamarin.Forms modelos de dados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Xamarin.Forms os modelos de dados fornecem a capacidade de definir a apresentação de dados em controles com suporte. Este artigo fornece uma introdução aos modelos de dados, examinando por que eles são necessários._

Considere um [`ListView`](xref:Xamarin.Forms.ListView) que exibe uma coleção de `Person` objetos. O exemplo de código a seguir mostra a definição da classe `Person`:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

A classe `Person` define as propriedades `Name`, `Age` e `Location`, que podem ser definidas quando um objeto `Person` é criado. O [`ListView`](xref:Xamarin.Forms.ListView) é usado para exibir a coleção de `Person` objetos, conforme mostrado no exemplo de código XAML a seguir:

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

Os itens são adicionados ao [`ListView`](xref:Xamarin.Forms.ListView) XAML in, inicializando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade de uma matriz de `Person` instâncias.

> [!NOTE]
> Observe que o elemento `x:Array` requer um atributo `Type` que indica o tipo dos itens na matriz.

A página C# equivalente é mostrada no exemplo de código a seguir, que inicializa a [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade para uma `List` das `Person` instâncias:

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

As [`ListView`](xref:Xamarin.Forms.ListView) chamadas `ToString` ao exibir os objetos na coleção. Como não há uma substituição de `Person.ToString`, `ToString` retorna o nome do tipo de cada objeto, conforme mostrado nas capturas de tela seguir:

![ListView sem um modelo de dados](introduction-images/no-data-template.png)

O objeto `Person` pode substituir o método `ToString` para exibir dados significativos, conforme mostrado no exemplo de código a seguir:

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

Isso resulta na [`ListView`](xref:Xamarin.Forms.ListView) exibição do `Person.Name` valor da propriedade para cada objeto na coleção, conforme mostrado nas capturas de tela a seguir:

![ListView com um modelo de dados](introduction-images/override-tostring.png)

A substituição `Person.ToString` poderia retornar uma cadeia de caracteres formatada composta pelas propriedades `Name`, `Age` e `Location`. No entanto, essa abordagem oferece apenas controle limitado sobre a aparência de cada item de dados. Para obter mais flexibilidade, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é possível criar um que defina a aparência dos dados.

## <a name="creating-a-datatemplate"></a>Criando um DataTemplate

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é usado para especificar a aparência dos dados e normalmente usa a vinculação de dados para exibir dados. Seu cenário de uso comum é ao exibir dados de uma coleção de objetos em um [`ListView`](xref:Xamarin.Forms.ListView) . Por exemplo, quando um `ListView` está associado a uma coleção de objetos `Person`, a propriedade `ListView.ItemTemplate` é definida como um `DataTemplate` que define a aparência de cada objeto `Person` no `ListView`. O `DataTemplate` conterá elementos que associam aos valores de propriedade de cada objeto `Person`. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pode ser usado como um valor para as seguintes propriedades:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), que é herdado pelo [`ListView`](xref:Xamarin.Forms.ListView) .
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), que é herdado por [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) , [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

> [!NOTE]
> Observe que, embora o [`TableView`](xref:Xamarin.Forms.TableView) faça uso de [`Cell`](xref:Xamarin.Forms.Cell) objetos, ele não usa um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Isso acontece porque as associações de dados sempre são definidas diretamente em objetos `Cell`.

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que é colocado como um filho direto das propriedades listadas acima é conhecido como um *modelo embutido*. Como alternativa, é possível definir um `DataTemplate` como um recurso de nível de controle, nível de página ou nível de aplicativo. Escolhendo onde definir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impacto onde ele pode ser usado:

- Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definido no nível de controle só pode ser aplicado ao controle.
- Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definido no nível da página pode ser aplicado a vários controles válidos na página.
- Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definido no nível do aplicativo pode ser aplicado a controles válidos em todo o aplicativo.

Modelos de dados posicionados mais baixo na hierarquia de exibição têm precedência sobre os definidos mais acima quando eles compartilham atributos de `x:Key`. Por exemplo, um modelo de dados no nível do aplicativo será substituído por um modelo de dados no nível da página e um modelo de dados no nível da página será substituído por um modelo de dados no nível de controle ou por um modelo de dados embutido.

## <a name="related-links"></a>Links Relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (amostra)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)