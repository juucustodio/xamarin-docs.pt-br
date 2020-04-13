---
title: Criando um DataTemplateSelector do Xamarin.Forms
description: Este artigo demonstra como criar e consumir um DataTemplateSelector, que pode ser usado para escolher um DataTemplate em runtime com base no valor de uma propriedade associada a dados.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9a17bff9bd0a23d59faf7602544b25c7ec05a86
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771251"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Criando um DataTemplateSelector do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Um DataTemplateSelector pode ser usado para escolher um DataTemplate em tempo de execução com base no valor de uma propriedade vinculada a dados. Isso permite que vários DataTemplates sejam aplicados ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos. Este artigo demonstra como criar e consumir um DataTemplateSelector._

Um seletor de modelo [`ListView`](xref:Xamarin.Forms.ListView) de dados permite cenários como uma vinculação a uma coleção de objetos, onde a aparência de cada objeto no `ListView` pode ser escolhido em tempo de execução pelo seletor de modelo de dados que retorna um determinado [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Criando um DataTemplateSelector

Um seletor de modelo de dados [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)é implementado criando uma classe que herda de . O `OnSelectTemplate` método é então substituído para [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)retornar um determinado, como mostrado no exemplo de código a seguir:

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

Uma instância da classe seletor de modelo de dados pode então [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)ser atribuída às propriedades de controle Xamarin.Forms, como . Para obter uma lista de propriedades válidas, confira [Criando um DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitações

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)as instâncias têm as seguintes limitações:

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

Este nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) página define `PersonDataTemplateSelector` duas instâncias e uma instância. A instância `PersonDataTemplateSelector` define suas propriedades `ValidTemplate` e `InvalidTemplate` como as instâncias `DataTemplate` apropriadas usando a extensão de marcação `StaticResource`. Observe que, embora os recursos sejam [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)definidos na página, eles também podem ser definidos no nível de controle ou aplicativo.

A `PersonDataTemplateSelector` instância é consumida atribuindo-a à [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade, como mostrado no exemplo de código a seguir:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Em tempo de [`ListView`](xref:Xamarin.Forms.ListView) execução, o método chama o `PersonDataTemplateSelector.OnSelectTemplate` método para cada um dos itens `item` da coleção subjacente, com a chamada passando o objeto de dados como parâmetro. O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que é devolvido pelo método é então aplicado a esse objeto.

As capturas de tela [`ListView`](xref:Xamarin.Forms.ListView) a seguir `PersonDataTemplateSelector` mostram o resultado da aplicação do objeto em cada objeto na coleção subjacente:

![](selector-images/data-template-selector.png "ListView with a Data Template Selector")

Qualquer objeto `Person` que tem o valor da propriedade `DateOfBirth` maior que ou igual a 1980 é exibido em verde, com os objetos restantes sendo exibidos em vermelho.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumindo um DataTemplateSelector em C&num;

Em C#, `PersonDataTemplateSelector` o pode ser instanciado [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) e atribuído à propriedade, conforme mostrado no exemplo de código a seguir:

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

A `PersonDataTemplateSelector` instância `ValidTemplate` define `InvalidTemplate` suas propriedades [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para as `SetupDataTemplates` instâncias apropriadas criadas pelo método. Em tempo de [`ListView`](xref:Xamarin.Forms.ListView) execução, o método chama o `PersonDataTemplateSelector.OnSelectTemplate` método para cada um dos itens `item` da coleção subjacente, com a chamada passando o objeto de dados como parâmetro. O `DataTemplate` retornado pelo método é aplicado a esse objeto.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)e consumir um . A `DataTemplateSelector` pode ser usado [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para escolher um em tempo de execução com base no valor de uma propriedade vinculada a dados. Isso permite que várias instâncias de `DataTemplate` sejam aplicadas ao mesmo tipo de objeto, para personalizar a aparência de objetos específicos.

## <a name="related-links"></a>Links relacionados

- [Seletor de Modelo de Dados (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
