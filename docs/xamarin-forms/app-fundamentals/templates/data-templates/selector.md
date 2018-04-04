---
title: Criando um DataTemplateSelector
description: Um DataTemplateSelector pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários DataTemplates a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos. Este artigo demonstra como criar e consumir um DataTemplateSelector.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d87b9f3cf47e3b3efa42ad53cfba91bac1d07d4f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplateselector"></a>Criando um DataTemplateSelector

_Um DataTemplateSelector pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários DataTemplates a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos. Este artigo demonstra como criar e consumir um DataTemplateSelector._

Um seletor de modelo de dados permite cenários como um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) associação a uma coleção de objetos, em que a aparência de cada objeto no `ListView` podem ser escolhidos em tempo de execução pelo seletor de modelo de dados retornando um específico [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/).

## <a name="creating-a-datatemplateselector"></a>Criando um DataTemplateSelector

Um seletor de modelo de dados é implementado, criando uma classe que herda de [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/). O `OnSelectTemplate` método é substituído, em seguida, para retornar um determinado [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), conforme mostrado no exemplo de código a seguir:

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

O `OnSelectTemplate` método retorna o modelo apropriado com base no valor da `DateOfBirth` propriedade. O modelo a ser retornado é o valor da `ValidTemplate` propriedade ou o `InvalidTemplate` propriedade, que são definidas quando o consumo de `PersonDataTemplateSelector`.

Uma instância da classe de seletor de modelo de dados pode ser atribuída para propriedades de controle do xamarin. Forms como [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/). Para obter uma lista de propriedades válidas, consulte [criando um DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitações

[`DataTemplateSelector`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) instâncias têm as seguintes limitações:

- O `DataTemplateSelector` subclasse sempre deve retornar o mesmo modelo para os mesmos dados se consultado várias vezes.
- O `DataTemplateSelector` subclasse não deve retornar a outro `DataTemplateSelector` subclasse.
- O `DataTemplateSelector` subclasse não deve retornar novas instâncias de uma `DataTemplate` em cada chamada. Em vez disso, a mesma instância deve ser retornada. Falha ao fazer isso criará um vazamento de memória e desabilitará a virtualização.
- No Android, pode haver mais do que 20 modelos de dados diferente por `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consumindo um DataTemplateSelector em XAML

Em XAML, o `PersonDataTemplateSelector` pode ser instanciado pelo declará-la como um recurso, conforme mostrado no exemplo de código a seguir:

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

Esse nível de página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define dois [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) instâncias e um `PersonDataTemplateSelector` instância. O `PersonDataTemplateSelector` instância define seu `ValidTemplate` e `InvalidTemplate` propriedades ao apropriado `DataTemplate` instâncias usando o `StaticResource` extensão de marcação. Observe que, enquanto os recursos são definidos na página de [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), também pode ser definidos no nível de controle ou no nível do aplicativo.

O `PersonDataTemplateSelector` instância é consumida, atribuindo-a para o [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propriedade, conforme mostrado no exemplo de código a seguir:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Em tempo de execução, o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) chama o `PersonDataTemplateSelector.OnSelectTemplate` método para cada um dos itens na coleção, com a chamada passando o objeto de dados como o `item` parâmetro. O [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que é retornado pelo método é então aplicado a esse objeto.

As capturas de tela a seguir mostram o resultado da [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) aplicando o `PersonDataTemplateSelector` para cada objeto na coleção:

![](selector-images/data-template-selector.png "ListView com um seletor de modelo de dados")

Qualquer `Person` objeto que tem um `DateOfBirth` valor da propriedade maior que ou igual a 1980 é exibida em verde, com os objetos restantes que está sendo exibidos em vermelho.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumindo um DataTemplateSelector em C&num;

No c#, o `PersonDataTemplateSelector` pode ser criada e atribuída ao [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propriedade, conforme mostrado no exemplo de código a seguir:

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

O `PersonDataTemplateSelector` instância define seu `ValidTemplate` e `InvalidTemplate` propriedades ao apropriado [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) instâncias criadas pelo `SetupDataTemplates` método. Em tempo de execução, o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) chama o `PersonDataTemplateSelector.OnSelectTemplate` método para cada um dos itens na coleção, com a chamada passando o objeto de dados como o `item` parâmetro. O `DataTemplate` que é retornado pelo método é então aplicado a esse objeto.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir um [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/). Um `DataTemplateSelector` pode ser usado para escolher um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) em tempo de execução com base no valor de uma propriedade de associação de dados. Isso permite que vários `DataTemplate` instâncias a serem aplicadas para o mesmo tipo de objeto, para personalizar a aparência de determinados objetos.


## <a name="related-links"></a>Links relacionados

- [Seletor de modelo de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)
