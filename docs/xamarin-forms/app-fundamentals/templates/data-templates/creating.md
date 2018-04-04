---
title: Criando um DataTemplate
description: Modelos de dados podem ser criados em linha, em um dicionário de recurso, ou de um tipo personalizado ou o tipo apropriado de célula xamarin. Forms. Este artigo explora cada técnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: cd4266fb8e7d68a9f93bd169ca70c6a0f516a357
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplate"></a>Criando um DataTemplate

_Modelos de dados podem ser criados em linha, em um dicionário de recurso, ou de um tipo personalizado ou o tipo apropriado de célula xamarin. Forms. Este artigo explora cada técnica._

Um cenário de uso comum para um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) está exibindo dados de uma coleção de objetos em um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). A aparência dos dados para cada célula de [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) podem ser gerenciados definindo o [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propriedade para um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Há várias técnicas que podem ser usados para fazer isso:

- [Criando um DataTemplate embutido](#inline).
- [Criando um DataTemplate com um tipo](#type).
- [Criando um DataTemplate como um recurso](#resource).

Independentemente da técnica que está sendo usada, o resultado é que, a aparência de cada célula de [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é definido por um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), conforme mostrado nas capturas de tela seguir:

![](creating-images/data-template-appearance.png "ListView com um DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Criando um DataTemplate embutido

O [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propriedade pode ser definida como um embutido [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Um modelo embutida, que é aquele que é inserido como um filho direto de uma propriedade de controle apropriado, deve ser usado se não houver nenhuma necessidade de reutilizar o modelo de dados em outro lugar. Os elementos especificados no `DataTemplate` definem a aparência de cada célula, conforme mostrado no exemplo de código XAML a seguir:

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

O filho de um embutido [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) deve ser de, ou derivam, digite [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/). Layout dentro de `ViewCell` é gerenciado aqui por uma [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). O `Grid` contém três [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias que associar sua [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedades para as propriedades adequadas de cada `Person` objeto na coleção.

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

No c#, embutido [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) é criado usando uma sobrecarga de construtor que especifica um `Func` argumento.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Criando um DataTemplate com um tipo

O [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propriedade também pode ser definida um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que é criado de um tipo de célula. A vantagem dessa abordagem é que a aparência definida pelo tipo de célula pode ser reutilizada por vários modelos de dados em todo o aplicativo. O código XAML a seguir mostra um exemplo dessa abordagem:

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

Aqui, o [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) está definida como um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que é criado a partir de um tipo personalizado que define a aparência da célula. O tipo personalizado deve derivar do tipo [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), conforme mostrado no exemplo de código a seguir:

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

Dentro de [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), layout é gerenciado aqui por uma [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). O `Grid` contém três [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias que associar sua [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedades para as propriedades adequadas de cada `Person` objeto na coleção.

O código c# equivalente é mostrado no exemplo a seguir:

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

No c#, o [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) é criado usando uma sobrecarga de construtor que especifica o tipo de célula como um argumento. O tipo de célula deve derivar do tipo [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), conforme mostrado no exemplo de código a seguir:

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
> Observe que o xamarin. Forms também inclui tipos de célula que podem ser usados para exibir dados simples em [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) células. Para obter mais informações, consulte [aparência célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Criando um DataTemplate como um recurso

Modelos de dados também podem ser criados como objetos reutilizáveis em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Isso é feito fornecendo uma única declaração de cada `x:Key` atributo, que fornece uma chave descritiva a `ResourceDictionary`, conforme mostrado no exemplo de código XAML a seguir:

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

O [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) é atribuído para o [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propriedade usando o `StaticResource` extensão de marcação. Observe que, embora o `DataTemplate` é definido na página de [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), também pode ser definido no nível de controle ou no nível do aplicativo.

O exemplo de código a seguir mostra a página equivalente em c#:

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

O [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) é adicionada para o [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) usando o [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) método, que especifica um `Key` cadeia de caracteres que é usada para referência a `DataTemplate` quando recuperá-los.

## <a name="summary"></a>Resumo

Este artigo explicou como criar modelos de dados em linha, de um tipo personalizado, ou em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Um modelo embutida deve ser usado se não houver nenhuma necessidade de reutilizar o modelo de dados em outro lugar. Como alternativa, um modelo de dados pode ser reutilizado por defini-lo como um tipo personalizado, ou como um recurso de nível de página ou o nível de aplicativo de nível de controle.


## <a name="related-links"></a>Links relacionados

- [Aparência de célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modelos de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
