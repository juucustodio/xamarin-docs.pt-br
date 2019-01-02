---
title: Introdução a modelos de dados do Xamarin.Forms
description: Os modelos de dados do Xamarin.Forms fornecem a capacidade de definir a apresentação de dados em controles compatíveis. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: bcea44688a64e741868e7ad2adf6c4dc65a5071a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052271"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introdução a modelos de dados do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)

_Os modelos de dados do Xamarin.Forms fornecem a capacidade de definir a apresentação de dados em controles compatíveis. Este artigo fornece uma introdução aos modelos de dados, examinando o motivo pelo qual eles são necessários._

Considere uma [`ListView`](xref:Xamarin.Forms.ListView) que exibe uma coleção de objetos `Person`. O exemplo de código a seguir mostra a definição da classe `Person`:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

A classe `Person` define as propriedades `Name`, `Age` e `Location`, que podem ser definidas quando um objeto `Person` é criado. O [`ListView`](xref:Xamarin.Forms.ListView) é usado para exibir a coleção de objetos `Person`, conforme mostrado no exemplo de código XAML a seguir:

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

Itens são adicionados ao [`ListView`](xref:Xamarin.Forms.ListView) no XAML inicializando a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) de uma matriz de instâncias de `Person`.

> [!NOTE]
> Observe que o elemento `x:Array` requer um atributo `Type` que indica o tipo dos itens na matriz.

A página em C# equivalente é mostrada no exemplo de código a seguir, que inicializa a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) para um `List` de instâncias de `Person`:

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

O [`ListView`](xref:Xamarin.Forms.ListView) chama `ToString` ao exibir os objetos na coleção. Como não há uma substituição de `Person.ToString`, `ToString` retorna o nome do tipo de cada objeto, conforme mostrado nas capturas de tela seguir:

![](introduction-images/no-data-template.png "ListView sem um modelo de dados")

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

Isso faz com que [`ListView`](xref:Xamarin.Forms.ListView) exiba o valor da propriedade `Person.Name` para cada objeto na coleção, conforme mostrado nas capturas de tela seguir:

![](introduction-images/override-tostring.png "ListView com um modelo de dados")

A substituição `Person.ToString` poderia retornar uma cadeia de caracteres formatada composta pelas propriedades `Name`, `Age` e `Location`. No entanto, essa abordagem oferece apenas controle limitado sobre a aparência de cada item de dados. Para ter mais flexibilidade, é possível criar um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência dos dados.

## <a name="creating-a-datatemplate"></a>Criando um DataTemplate

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é usado para especificar a aparência dos dados e normalmente usa a associação de dados para exibir dados. Um cenário de uso comum é exibir dados de uma coleção de objetos em um [`ListView`](xref:Xamarin.Forms.ListView). Por exemplo, quando um `ListView` está associado a uma coleção de objetos `Person`, a propriedade `ListView.ItemTemplate` é definida como um `DataTemplate` que define a aparência de cada objeto `Person` no `ListView`. O `DataTemplate` conterá elementos que associam aos valores de propriedade de cada objeto `Person`. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pode ser usado como um valor para as seguintes propriedades:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), que é herdado por [`ListView`](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), que é herdado por [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage).

> [!NOTE]
> Observe que, embora o [`TableView`](xref:Xamarin.Forms.TableView) use objetos [`Cell`](xref:Xamarin.Forms.Cell), ele não usa um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Isso acontece porque as associações de dados sempre são definidas diretamente em objetos `Cell`.

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que é colocado como filho direto das propriedades listadas acima é conhecido como um *modelo embutido*. Como alternativa, é possível definir um `DataTemplate` como um recurso de nível de controle, nível de página ou nível de aplicativo. Escolher onde definir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) afeta onde ele pode ser usado:

- Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definido no nível do controle só pode ser aplicado ao controle.
- Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definido no nível da página só pode ser aplicado a vários controles válidos na página.
- Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definido no nível do aplicativo pode ser aos controles válidos em todo o aplicativo.

Modelos de dados posicionados mais baixo na hierarquia de exibição têm precedência sobre os definidos mais acima quando eles compartilham atributos de `x:Key`. Por exemplo, um modelo de dados no nível do aplicativo será substituído por um modelo de dados no nível da página e um modelo de dados no nível da página será substituído por um modelo de dados no nível de controle ou por um modelo de dados embutido.


## <a name="related-links"></a>Links relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
