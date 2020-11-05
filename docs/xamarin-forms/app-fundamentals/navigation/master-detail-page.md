---
title: Xamarin.Forms Página de Master-Detail
description: O Xamarin.Forms MasterDetailPage é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar uma MasterDetailPage e navegar entre suas páginas de informações.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46f88b1672690645a8e71aba2c0dbe44d7b123cd
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368759"
---
# <a name="no-locxamarinforms-master-detail-page"></a>Xamarin.Forms Página de Master-Detail

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_O Xamarin.Forms MasterDetailPage é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar um MasterDetailPage e navegar entre suas páginas de informações._

## <a name="overview"></a>Visão geral

Uma página mestra normalmente exibe uma lista de itens, conforme mostrado nas capturas de tela seguir:

[![Componentes da página mestra](master-detail-page-images/masterpage-components.png)](master-detail-page-images/masterpage-components-large.png#lightbox "Componentes da página mestra")

A localização da lista de itens é idêntico em cada plataforma e selecionar um dos itens navegará até a página de detalhes correspondente. Além disso, a página mestra também apresenta uma barra de navegação que contém um botão que pode ser usado para navegar até a página de detalhes ativa:

- No iOS, a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda.
- No Android, a barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que navega até a página de detalhes. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda, tocando a página de detalhes na extremidade direita da tela e tocando no botão *Voltar* na parte inferior da tela.
- Na UWP (Plataforma Universal do Windows), a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes.

Uma página de detalhes exibe dados correspondentes ao item selecionado na página mestra e os principais componentes da página de detalhes são mostrados nas capturas de tela a seguir:

![Componentes da página de detalhes](master-detail-page-images/detailpage-components.png)

A página de detalhes contém uma barra de navegação, cujo conteúdo dependem da plataforma:

- No iOS, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna à página mestra, desde que a instância da página de detalhes seja encapsulada na [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância. Além disso, é possível retornar à página mestra passando o dedo na página de detalhes para a direita.
- No Android, uma barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que retorna à página mestra. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- Na UWP, uma barra de navegação está presente na parte superior da página e exibe um título e tem botão que retorna à página mestra.

### <a name="navigation-behavior"></a>Comportamento de navegação

O comportamento da experiência de navegação entre páginas mestras e de detalhes depende da plataforma:

- No iOS, a página de detalhes *desliza* para a direita enquanto a página mestra desliza para a esquerda e a parte esquerda da página de detalhes ainda está visível.
- No Android, as páginas mestras e de detalhes são *sobrepostas* umas às outras.
- No UWP, a página mestra desliza da esquerda sobre parte da página de detalhes, desde que a [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) Propriedade esteja definida como `Popover` . Para obter mais informações, confira [Controlando o comportamento de exibição da página de detalhes](#controlling-the-detail-page-display-behavior).

Um comportamento semelhante será observado no modo paisagem, exceto que a página mestra no iOS e no Android tem uma largura semelhante à da página mestra no modo retrato; portanto, mais coisas da página de detalhes ficarão visíveis.

Para saber como controlar o comportamento de navegação, confira [Controlling the Detail Page Display Behavior](#controlling-the-detail-page-display-behavior) (Controlando o comportamento de exibição da página de detalhes).

## <a name="creating-a-masterdetailpage"></a>Criando um MasterDetailPage

Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) Contains [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) Propriedades que são do tipo [`Page`](xref:Xamarin.Forms.Page) , que são usadas para obter e definir as páginas mestras e de detalhes respectivamente.

> [!IMPORTANT]
> Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) é projetado para ser uma página raiz e usá-lo como uma página filho em outros tipos de página pode resultar em comportamento inesperado e inconsistente. Além disso, é recomendável que a página mestra de a [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) sempre seja uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) instância e que a página de detalhes só deva ser populada [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) com [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instâncias, e `ContentPage` . Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

O exemplo de código XAML a seguir mostra um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) que define as [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) Propriedades e:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

O exemplo de código a seguir mostra o equivalente [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) criado em C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

A [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriedade é definida como uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) instância. A [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade é definida como um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contém uma `ContentPage` instância.

### <a name="creating-the-master-page"></a>Criando a página mestra

O exemplo de código XAML a seguir mostra a declaração do `MasterPage` objeto, que é referenciada por meio da [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) Propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

A página consiste em uma [`ListView`](xref:Xamarin.Forms.ListView) que é preenchida com dados em XAML, definindo sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade como uma matriz de `MasterPageItem` instâncias. Cada `MasterPageItem` define propriedades `Title`, `IconSource` e `TargetType`.

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído à [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade, para exibir cada `MasterPageItem` . O `DataTemplate` contém um [`ViewCell`](xref:Xamarin.Forms.ViewCell) que consiste em um [`Image`](xref:Xamarin.Forms.Image) e um [`Label`](xref:Xamarin.Forms.Label) . O [`Image`](xref:Xamarin.Forms.Image) exibe o `IconSource` valor da propriedade e [`Label`](xref:Xamarin.Forms.Label) exibe o `Title` valor da propriedade para cada um `MasterPageItem` .

A página tem suas [`Title`](xref:Xamarin.Forms.Page.Title) [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) Propriedades e definidas. O ícone será exibido na página de detalhes, desde que ela tenha uma barra de título. Isso deve ser habilitado no iOS encapsulando a instância da página de detalhes em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância.

> [!NOTE]
> A [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) página deve ter sua [`Title`](xref:Xamarin.Forms.Page.Title) propriedade definida ou ocorrerá uma exceção.

O exemplo de código a seguir mostra a página equivalente criada em C#:

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    IconImageSource = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

As capturas de tela a seguir mostram a página mestra em cada plataforma:

![Exemplo de página mestra](master-detail-page-images/masterpage.png)

### <a name="creating-and-displaying-the-detail-page"></a>Criação e exibição da página de detalhes

A `MasterPage` instância contém uma `ListView` propriedade que expõe sua [`ListView`](xref:Xamarin.Forms.ListView) instância para que a `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) instância possa registrar um manipulador de eventos para manipular o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento. Isso permite que a `MainPage` instância defina a [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade para a página que representa o `ListView` item selecionado. O exemplo de código a seguir mostra o manipulador de eventos:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

O método `OnItemSelected` executa as seguintes ações:

- Ele recupera o [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) da [`ListView`](xref:Xamarin.Forms.ListView) instância do e, desde que não esteja `null` , define a página de detalhes como uma nova instância do tipo de página armazenada na `TargetType` Propriedade do `MasterPageItem` . O tipo de página é encapsulado em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância para garantir que o ícone referenciado por meio da [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propriedade no `MasterPage` é mostrado na página de detalhes no Ios.
- O item selecionado no [`ListView`](xref:Xamarin.Forms.ListView) é definido como `null` para garantir que nenhum dos itens seja `ListView` selecionado na próxima vez em que `MasterPage` for apresentado.
- A página de detalhes é apresentada ao usuário definindo a [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriedade como `false` . Esta propriedade controla se a página mestra ou de detalhes é apresentada. Ela deve ser definida como `true` para exibir a página mestra e como `false` para exibir a página de detalhes.

As capturas de tela a seguir mostram a página de detalhes `ContactPage`, mostrada depois que ela for selecionada na página mestra:

![Exemplo de página de detalhes](master-detail-page-images/detailpage.png)

### <a name="controlling-the-detail-page-display-behavior"></a>Controle do comportamento de exibição da página de detalhes

Como o [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia as páginas mestras e de detalhes depende se o aplicativo está sendo executado em um telefone ou Tablet, a orientação do dispositivo e o valor da [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade. Essa propriedade determina como a página de detalhes será exibida. Os valores possíveis são:

- **Default** – as páginas são exibidas usando o padrão da plataforma.
- **Popover** – a página de detalhes cobre ou cobre parcialmente a página mestra.
- **Split** – a página mestra é exibida à esquerda e a página de detalhes está à direita.
- **SplitOnLandscape** – Uma tela dividida é usada quando o dispositivo está na orientação paisagem.
- **SplitOnPortrait** – Uma tela dividida é usada quando o dispositivo está na orientação retrato.

O exemplo de código XAML a seguir demonstra como definir a [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade em um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

O exemplo de código a seguir mostra o equivalente [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) criado em C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

No entanto, o valor da [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade afeta apenas os aplicativos em execução nos tablets ou na área de trabalho. Aplicativos em execução nos telefones sempre têm o comportamento *Popover*.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e navegar entre suas páginas de informações. O Xamarin.Forms `MasterDetailPage` é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.

## <a name="related-links"></a>Links Relacionados

- [Variedades de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemplo)](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)