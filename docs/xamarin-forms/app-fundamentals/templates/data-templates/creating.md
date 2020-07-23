---
title: Criando um Xamarin.Forms DataTemplate
description: Os modelos de dados podem ser criados embutidos, em um ResourceDictionary ou de um tipo personalizado ou de um Xamarin.Forms tipo de célula apropriado. Este artigo explora cada técnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9ed7e485adbc13862ff6fb6af9b02c43cc6a1f10
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935636"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Criando um Xamarin.Forms DataTemplate

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Os modelos de dados podem ser criados embutidos, em um ResourceDictionary ou de um tipo personalizado ou de um Xamarin.Forms tipo de célula apropriado. Este artigo explora cada técnica._

Um cenário de uso comum para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é a exibição de dados de uma coleção de objetos em um [`ListView`](xref:Xamarin.Forms.ListView) . A aparência dos dados para cada célula no [`ListView`](xref:Xamarin.Forms.ListView) pode ser gerenciada definindo a [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Há várias técnicas que podem ser usadas para fazer isso:

- [Criando um DataTemplate embutido](#creating-an-inline-datatemplate).
- [Criando um DataTemplate com um tipo](#creating-a-datatemplate-with-a-type).
- [Criando um DataTemplate como um recurso](#creating-a-datatemplate-as-a-resource).

Independentemente da técnica que está sendo usada, o resultado é que a aparência de cada célula no [`ListView`](xref:Xamarin.Forms.ListView) é definida por um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , conforme mostrado nas seguintes capturas de tela:

![ListView com um DataTemplate](creating-images/data-template-appearance.png)

## <a name="creating-an-inline-datatemplate"></a>Criando um DataTemplate embutido

A [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade pode ser definida como embutida [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Um modelo embutido, que é inserido como um filho direto de uma propriedade de controle apropriada, deverá ser usado se não houver necessidade de reutilizar o modelo de dados em outro lugar. Os elementos especificados no `DataTemplate` definem a aparência de cada célula, conforme mostrado no exemplo de código XAML a seguir:

```xaml
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
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

O filho de um embutido [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) deve ser de, ou derivar de, Type [`Cell`](xref:Xamarin.Forms.Cell) . Este exemplo usa um [`ViewCell`](xref:Xamarin.Forms.ViewCell) , que deriva de `Cell` . O layout dentro do `ViewCell` é gerenciado aqui por um [`Grid`](xref:Xamarin.Forms.Grid) . O `Grid` contém três [`Label`](xref:Xamarin.Forms.Label) instâncias que associam suas [`Text`](xref:Xamarin.Forms.Label.Text) Propriedades às propriedades apropriadas de cada `Person` objeto na coleção.

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

No C#, o embutido [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é criado usando uma sobrecarga de construtor que especifica um `Func` argumento.

## <a name="creating-a-datatemplate-with-a-type"></a>Criando um DataTemplate com um tipo

A [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade também pode ser definida como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que é criada a partir de um tipo de célula. A vantagem dessa abordagem é que a aparência definida pelo tipo de célula pode ser reutilizado por vários modelos de dados em todo o aplicativo. O código XAML a seguir mostra um exemplo dessa abordagem:

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
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Aqui, a [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade é definida como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que é criada com base em um tipo personalizado que define a aparência da célula. O tipo personalizado deve derivar do tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell) , conforme mostrado no exemplo de código a seguir:

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

No [`ViewCell`](xref:Xamarin.Forms.ViewCell) , o layout é gerenciado aqui por um [`Grid`](xref:Xamarin.Forms.Grid) . O `Grid` contém três [`Label`](xref:Xamarin.Forms.Label) instâncias que associam suas [`Text`](xref:Xamarin.Forms.Label.Text) Propriedades às propriedades apropriadas de cada `Person` objeto na coleção.

O código C# equivalente é mostrado no exemplo a seguir:

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

No C#, o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é criado usando uma sobrecarga de construtor que especifica o tipo de célula como um argumento. O tipo de célula deve derivar de tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell) , conforme mostrado no exemplo de código a seguir:

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> Observe que Xamarin.Forms também inclui tipos de célula que podem ser usados para exibir dados simples em [`ListView`](xref:Xamarin.Forms.ListView) células. Para saber mais, confira [Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Aparência da célula).

## <a name="creating-a-datatemplate-as-a-resource"></a>Criando um DataTemplate como um recurso

Os modelos de dados também podem ser criados como objetos reutilizáveis em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Isso é feito dando a cada declaração um atributo `x:Key` exclusivo, que fornece a ela uma chave descritiva no `ResourceDictionary`, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído à [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade usando a extensão de `StaticResource` marcação. Observe que, embora o `DataTemplate` esteja definido na página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , ele também pode ser definido no nível de controle ou do aplicativo.

O exemplo de código a seguir mostra a página equivalente em C#:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é adicionado ao [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) usando o [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, que especifica uma `Key` cadeia de caracteres que é usada para referenciar o `DataTemplate` ao recuperá-lo.

## <a name="summary"></a>Resumo

Este artigo explicou como criar modelos de dados, embutidos, de um tipo personalizado ou em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Um modelo embutido deve ser usado caso não haja necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.

## <a name="related-links"></a>Links Relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
