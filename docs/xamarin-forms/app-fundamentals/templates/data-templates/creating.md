---
title: Criando um DataTemplate do Xamarin.Forms
description: Os modelos de dados podem ser criados embutidos, em um ResourceDictionary ou de um tipo personalizado ou um tipo de célula apropriado do Xamarin.Forms. Este artigo explora cada técnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 1163f264fc54a461d8d95854524439589cdc81f5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646978"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Criando um DataTemplate do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Os modelos de dados podem ser criados embutidos, em um ResourceDictionary ou de um tipo personalizado ou um tipo de célula apropriado do Xamarin.Forms. Este artigo explora cada técnica._

Um cenário de uso comum para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é exibir dados de uma coleção de objetos em um [`ListView`](xref:Xamarin.Forms.ListView). A aparência dos dados para cada célula no [`ListView`](xref:Xamarin.Forms.ListView) pode ser gerenciada definindo a propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Há várias técnicas que podem ser usadas para fazer isso:

- [Criando um DataTemplate embutido](#inline).
- [Criando um DataTemplate com um tipo](#type).
- [Criando um DataTemplate como um recurso](#resource).

Independentemente da técnica que está sendo usada, o resultado é que a aparência de cada célula no [`ListView`](xref:Xamarin.Forms.ListView) é definida por um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), conforme mostrado nas seguintes capturas de tela:

![](creating-images/data-template-appearance.png "ListView com um DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Criando um DataTemplate embutido

A propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) pode ser definida como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) embutido. Um modelo embutido, que é inserido como um filho direto de uma propriedade de controle apropriada, deverá ser usado se não houver necessidade de reutilizar o modelo de dados em outro lugar. Os elementos especificados no `DataTemplate` definem a aparência de cada célula, conforme mostrado no exemplo de código XAML a seguir:

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

O filho de um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) embutido deve ser do tipo [`Cell`](xref:Xamarin.Forms.Cell) ou derivado dele. Este exemplo usa um [`ViewCell`](xref:Xamarin.Forms.ViewCell), que deriva de `Cell`. O layout dentro do `ViewCell` é gerenciado aqui por um [`Grid`](xref:Xamarin.Forms.Grid). O `Grid` contém três instâncias [`Label`](xref:Xamarin.Forms.Label) que associam suas propriedades [`Text`](xref:Xamarin.Forms.Label.Text) às propriedades adequadas de cada objeto `Person` na coleção.

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

Em C#, o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) embutido é criado usando uma sobrecarga de construtor que especifica um argumento `Func`.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Criando um DataTemplate com um tipo

A propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) também pode ser definida como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) criada com base em um tipo de célula. A vantagem dessa abordagem é que a aparência definida pelo tipo de célula pode ser reutilizado por vários modelos de dados em todo o aplicativo. O código XAML a seguir mostra um exemplo dessa abordagem:

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

Aqui, a propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) é definida como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) criado com base em um tipo personalizado que define a aparência da célula. O tipo personalizado deve ser derivado do tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell), conforme mostrado no exemplo de código a seguir:

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

Dentro do [`ViewCell`](xref:Xamarin.Forms.ViewCell), o layout é gerenciado aqui por um [`Grid`](xref:Xamarin.Forms.Grid). O `Grid` contém três instâncias [`Label`](xref:Xamarin.Forms.Label) que associam suas propriedades [`Text`](xref:Xamarin.Forms.Label.Text) às propriedades adequadas de cada objeto `Person` na coleção.

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

Em C#, o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é criado usando uma sobrecarga de construtor que especifica o tipo de célula como um argumento. O tipo de célula deve ser derivado do tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell), conforme mostrado no exemplo de código a seguir:

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
> Observe que o Xamarin.Forms também inclui tipos de células que podem ser usados para exibir dados simples em células [`ListView`](xref:Xamarin.Forms.ListView). Para saber mais, confira [Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Aparência da célula).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Criando um DataTemplate como um recurso

Os modelos de dados também podem ser criados como objetos reutilizáveis em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Isso é feito dando a cada declaração um atributo `x:Key` exclusivo, que fornece a ela uma chave descritiva no `ResourceDictionary`, conforme mostrado no exemplo de código XAML a seguir:

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

O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído à propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) usando a extensão de marcação `StaticResource`. Observe que, embora o `DataTemplate` seja definido no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) da página, ele também poderia ser definido no nível do controle ou do aplicativo.

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

O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é adicionado ao [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) usando o método [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)), que especifica uma cadeia de caracteres `Key` usada para referenciar o `DataTemplate` ao recuperá-la.

## <a name="summary"></a>Resumo

Este artigo explicou como criar modelos de dados, embutidos, com base em um tipo personalizado ou em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Um modelo embutido deve ser usado caso não haja necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.


## <a name="related-links"></a>Links relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
