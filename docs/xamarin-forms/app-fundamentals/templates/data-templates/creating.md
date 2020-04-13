---
title: Criando um DataTemplate do Xamarin.Forms
description: Os modelos de dados podem ser criados embutidos, em um ResourceDictionary ou de um tipo personalizado ou um tipo de célula apropriado do Xamarin.Forms. Este artigo explora cada técnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 399f411acd497b9d55ca81f670556430fe5f5503
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771294"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Criando um DataTemplate do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Os modelos de dados podem ser criados inline, em um ResourceDictionary, ou a partir de um tipo personalizado ou do tipo de célula Xamarin.Forms apropriado. Este artigo explora cada técnica._

Um cenário de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) uso comum para um é [`ListView`](xref:Xamarin.Forms.ListView)exibir dados de uma coleção de objetos em um . O aparecimento dos dados de [`ListView`](xref:Xamarin.Forms.ListView) cada célula no pode [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) ser [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)gerenciado definindo a propriedade como um . Há várias técnicas que podem ser usadas para fazer isso:

- [Criando um DataTemplate embutido](#inline).
- [Criando um DataTemplate com um Tipo](#type).
- [Criando um DataTemplate como recurso](#resource).

Independentemente da técnica utilizada, o resultado é que [`ListView`](xref:Xamarin.Forms.ListView) o aparecimento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)de cada célula no é definido por a , como mostrado nas seguintes capturas de tela:

![](creating-images/data-template-appearance.png "ListView with a DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Criando um DataTemplate embutido

A [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade pode ser definida [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)como uma linha. Um modelo embutido, que é inserido como um filho direto de uma propriedade de controle apropriada, deverá ser usado se não houver necessidade de reutilizar o modelo de dados em outro lugar. Os elementos especificados no `DataTemplate` definem a aparência de cada célula, conforme mostrado no exemplo de código XAML a seguir:

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

O filho de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) um inline deve ser [`Cell`](xref:Xamarin.Forms.Cell)de, ou derivar de, tipo . Este exemplo [`ViewCell`](xref:Xamarin.Forms.ViewCell)usa um , `Cell`que deriva de . Layout dentro `ViewCell` do é gerenciado [`Grid`](xref:Xamarin.Forms.Grid)aqui por um . As `Grid` três [`Label`](xref:Xamarin.Forms.Label) instâncias [`Text`](xref:Xamarin.Forms.Label.Text) que vinculam suas `Person` propriedades às propriedades apropriadas de cada objeto na coleção.

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

Em C#, a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) linha inline é criada usando `Func` uma sobrecarga de construtor que especifica um argumento.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Criando um DataTemplate com um tipo

A [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriedade também pode [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ser definida como uma que é criada a partir de um tipo de célula. A vantagem dessa abordagem é que a aparência definida pelo tipo de célula pode ser reutilizado por vários modelos de dados em todo o aplicativo. O código XAML a seguir mostra um exemplo dessa abordagem:

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

Aqui, [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) a propriedade é [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definida como uma que é criada a partir de um tipo personalizado que define a aparência da célula. O tipo personalizado deve [`ViewCell`](xref:Xamarin.Forms.ViewCell)derivar do tipo, conforme mostrado no exemplo de código a seguir:

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

Dentro [`ViewCell`](xref:Xamarin.Forms.ViewCell)do layout é gerenciado [`Grid`](xref:Xamarin.Forms.Grid)aqui por um . As `Grid` três [`Label`](xref:Xamarin.Forms.Label) instâncias [`Text`](xref:Xamarin.Forms.Label.Text) que vinculam suas `Person` propriedades às propriedades apropriadas de cada objeto na coleção.

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

Em C#, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) o é criado usando uma sobrecarga de construtor que especifica o tipo de célula como argumento. O tipo de célula [`ViewCell`](xref:Xamarin.Forms.ViewCell)deve derivar do tipo, conforme mostrado no exemplo de código a seguir:

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
> Observe que xamarin.forms também inclui tipos de células [`ListView`](xref:Xamarin.Forms.ListView) que podem ser usadas para exibir dados simples em células. Para saber mais, confira [Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Aparência da célula).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Criando um DataTemplate como um recurso

Modelos de dados também podem ser criados [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)como objetos reutilizáveis em um . Isso é feito dando a cada declaração um atributo `x:Key` exclusivo, que fornece a ela uma chave descritiva no `ResourceDictionary`, conforme mostrado no exemplo de código XAML a seguir:

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

O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) à propriedade `StaticResource` usando a extensão de marcação. Observe que, `DataTemplate` embora o seja [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)definido na página, ele também pode ser definido no nível de controle ou aplicativo.

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

O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é adicionado [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ao [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) uso do método, que especifica `Key` uma `DataTemplate` string que é usada para referenciar o quando recuperá-lo.

## <a name="summary"></a>Resumo

Este artigo explicou como criar modelos de dados, inline, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a partir de um tipo personalizado, ou em um . Um modelo embutido deve ser usado caso não haja necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado, definindo-o como um tipo personalizado ou como um recurso de aplicativo de nível de controle, de página ou de aplicativo.

## <a name="related-links"></a>Links relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
