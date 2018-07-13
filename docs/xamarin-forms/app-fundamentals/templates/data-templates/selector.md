---
title: Criando um DataTemplateSelector xamarin. Forms
description: Este artigo demonstra como criar e consumir um DataTemplateSelector, que pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade de associação de dados.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994511"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Criando um DataTemplateSelector xamarin. Forms

_Um DataTemplateSelector pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários DataTemplates a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos. Este artigo demonstra como criar e consumir um DataTemplateSelector._

Um seletor de modelo de dados permite cenários como um [ `ListView` ](xref:Xamarin.Forms.ListView) associando a uma coleção de objetos, no qual a aparência de cada objeto no `ListView` pode ser escolhido no tempo de execução pelo seletor de modelo de dados retornando um particular [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Criando um DataTemplateSelector

Um seletor de modelo de dados é implementado, criando uma classe que herda de [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). O `OnSelectTemplate` método, em seguida, é substituído para retornar um determinado [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), conforme mostrado no exemplo de código a seguir:

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

O `OnSelectTemplate` método retorna o modelo apropriado com base no valor da `DateOfBirth` propriedade. O modelo a ser retornado é o valor da `ValidTemplate` propriedade ou o `InvalidTemplate` propriedade, que são definidos ao consumir o `PersonDataTemplateSelector`.

Uma instância da classe de seletor de modelo de dados pode ser atribuída a propriedades de controle do xamarin. Forms, como [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1). Para obter uma lista de propriedades válidas, consulte [criando um DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitações

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) instâncias têm as seguintes limitações:

- O `DataTemplateSelector` subclasse deve sempre retornar o mesmo modelo para os mesmos dados se consultado várias vezes.
- O `DataTemplateSelector` subclasse não deve retornar outro `DataTemplateSelector` subclasse.
- O `DataTemplateSelector` subclasse não deve retornar novas instâncias de um `DataTemplate` em cada chamada. Em vez disso, a mesma instância deve ser retornada. Não fizer isso, criará um vazamento de memória e desabilita a virtualização.
- No Android, pode haver modelos de dados diferentes não mais de 20 por `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consumindo um DataTemplateSelector em XAML

No XAML, o `PersonDataTemplateSelector` pode ser instanciada ao declará-la como um recurso, conforme mostrado no exemplo de código a seguir:

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

Esse nível de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define dois [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) instâncias e um `PersonDataTemplateSelector` instância. O `PersonDataTemplateSelector` conjuntos de instância seus `ValidTemplate` e `InvalidTemplate` propriedades ao apropriado `DataTemplate` instâncias usando o `StaticResource` extensão de marcação. Observe que, enquanto os recursos, são definidos na página de [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), também pode ser definidos no nível de controle ou no nível do aplicativo.

O `PersonDataTemplateSelector` instância é consumida, atribuindo-o para o [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propriedade, conforme mostrado no exemplo de código a seguir:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Em tempo de execução, o [ `ListView` ](xref:Xamarin.Forms.ListView) chamadas a `PersonDataTemplateSelector.OnSelectTemplate` método para cada um dos itens na coleção subjacente, com a chamada passando o objeto de dados como o `item` parâmetro. O [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que é retornado pelo método, em seguida, é aplicado a esse objeto.

As capturas de tela a seguir mostram o resultado do [ `ListView` ](xref:Xamarin.Forms.ListView) aplicando o `PersonDataTemplateSelector` para cada objeto na coleção subjacente:

![](selector-images/data-template-selector.png "ListView com um seletor de modelo de dados")

Qualquer `Person` objeto que tem um `DateOfBirth` valor da propriedade maior que ou igual a 1980 é exibida em verde, com os objetos restantes que está sendo exibidos em vermelho.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumindo um DataTemplateSelector em C&num;

No c#, o `PersonDataTemplateSelector` pode ser instanciado e atribuído para o [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propriedade, conforme mostrado no exemplo de código a seguir:

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

O `PersonDataTemplateSelector` conjuntos de instância seus `ValidTemplate` e `InvalidTemplate` propriedades ao apropriado [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) instâncias criadas pelo `SetupDataTemplates` método. Em tempo de execução, o [ `ListView` ](xref:Xamarin.Forms.ListView) chamadas a `PersonDataTemplateSelector.OnSelectTemplate` método para cada um dos itens na coleção subjacente, com a chamada passando o objeto de dados como o `item` parâmetro. O `DataTemplate` que é retornado pelo método, em seguida, é aplicado a esse objeto.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). Um `DataTemplateSelector` pode ser usada para escolher uma [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários `DataTemplate` instâncias a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos.


## <a name="related-links"></a>Links relacionados

- [Seletor de modelo de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
