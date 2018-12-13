---
title: Criando um DataTemplateSelector do Xamarin.Forms
description: Este artigo demonstra como criar e consumir um DataTemplateSelector, que pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade associada a dados.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994511"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Criando um DataTemplateSelector do Xamarin.Forms

_Um DataTemplateSelector pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade associada a dados. Isso permite que várias instâncias de DataTemplates sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos. Este artigo demonstra como criar e consumir um DataTemplateSelector._

Um seletor de modelo de dados permite cenários como uma associação do [`ListView`](xref:Xamarin.Forms.ListView) a uma coleção de objetos, em que a aparência de cada objeto no `ListView` pode ser escolhida em tempo de execução pelo seletor de modelo de dados que retorna um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) específico.

## <a name="creating-a-datatemplateselector"></a>Criando um DataTemplateSelector

Um seletor de modelo de dados é implementado criando uma classe herdada de [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). O método `OnSelectTemplate` é substituído para retornar um determinado [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), conforme mostrado no exemplo de código a seguir:

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

O método `OnSelectTemplate` retorna o modelo apropriado com base no valor da propriedade `DateOfBirth`. O modelo a ser retornado é o valor da propriedade `ValidTemplate` ou da propriedade `InvalidTemplate`, que são definidas ao consumir o `PersonDataTemplateSelector`.

Uma instância da classe de seletor de modelo de dados pode ser atribuída às propriedades de controle do Xamarin.Forms, como [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1). Para obter uma lista de propriedades válidas, confira [Criando um DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitações

As instâncias [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) têm as seguintes limitações:

- A subclasse `DataTemplateSelector` deve sempre retornar o mesmo modelo para os mesmos dados se consultada várias vezes.
- A subclasse `DataTemplateSelector` não deve retornar outra subclasse `DataTemplateSelector`.
- A subclasse `DataTemplateSelector` não deve retornar novas instâncias de um `DataTemplate` em cada chamada. Em vez disso, a mesma instância deve ser retornada. Não fazer isso criará uma perda de memória e desabilitará a virtualização.
- No Android, não pode haver mais de 20 modelos de dados diferentes por `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consumindo um DataTemplateSelector em XAML

Em XAML, o `PersonDataTemplateSelector` pode ser instanciado ao declará-lo como um recurso, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

Esse nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define duas instâncias [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) e uma instância `PersonDataTemplateSelector`. A instância `PersonDataTemplateSelector` define suas propriedades `ValidTemplate` e `InvalidTemplate` como as instâncias `DataTemplate` apropriadas usando a extensão de marcação `StaticResource`. Observe que, embora os recursos sejam definidos no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) da página, eles também podem ser definidos no nível de controle ou do aplicativo.

A instância `PersonDataTemplateSelector` é consumida atribuindo-a à propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), conforme mostrado no exemplo de código a seguir:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Em tempo de execução, o [`ListView`](xref:Xamarin.Forms.ListView) chama o método `PersonDataTemplateSelector.OnSelectTemplate` para cada um dos itens na coleção subjacente, com a chamada passando o objeto de dados como o parâmetro `item`. O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) retornado pelo método é aplicado a esse objeto.

As capturas de tela a seguir mostram o resultado do [`ListView`](xref:Xamarin.Forms.ListView) aplicando o `PersonDataTemplateSelector` a cada objeto na coleção subjacente:

![](selector-images/data-template-selector.png "ListView com um Seletor de Modelo de Dados")

Qualquer objeto `Person` que tem o valor da propriedade `DateOfBirth` maior que ou igual a 1980 é exibido em verde, com os objetos restantes sendo exibidos em vermelho.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumindo um DataTemplateSelector em C&num;

Em C#, o `PersonDataTemplateSelector` pode ser instanciado e atribuído à propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), conforme mostrado no exemplo de código a seguir:

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

A instância `PersonDataTemplateSelector` define suas propriedades `ValidTemplate` e `InvalidTemplate` como as instâncias [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) apropriadas pelo método `SetupDataTemplates`. Em tempo de execução, o [`ListView`](xref:Xamarin.Forms.ListView) chama o método `PersonDataTemplateSelector.OnSelectTemplate` para cada um dos itens na coleção subjacente, com a chamada passando o objeto de dados como o parâmetro `item`. O `DataTemplate` retornado pelo método é aplicado a esse objeto.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). Um `DataTemplateSelector` pode ser usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) em tempo de execução com base no valor de uma propriedade associada a dados. Isso permite que várias instâncias de `DataTemplate` sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos.


## <a name="related-links"></a>Links relacionados

- [Seletor de Modelo de Dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
