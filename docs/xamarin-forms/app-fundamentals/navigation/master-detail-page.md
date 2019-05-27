---
title: Página Mestre/Detalhes do Xamarin.Forms
description: A MasterDetailPage do Xamarin.Forms é uma página que gerencia duas páginas relacionadas de informações – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar uma MasterDetailPage e navegar entre suas páginas de informações.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c60eecd5ebd0f518514cd77d8dd963568d1a1d43
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970910"
---
# <a name="xamarinforms-master-detail-page"></a>Página Mestre/Detalhes do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)

_A MasterDetailPage do Xamarin.Forms é uma página que gerencia duas páginas relacionadas de informações – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar uma MasterDetailPage e navegar entre suas páginas de informações._

## <a name="overview"></a>Visão geral

Uma página mestra normalmente exibe uma lista de itens, conforme mostrado nas capturas de tela seguir:

[![](master-detail-page-images/masterpage-components.png "Componentes da página mestra")](master-detail-page-images/masterpage-components-large.png#lightbox "Componentes da página mestra")

A localização da lista de itens é idêntico em cada plataforma e selecionar um dos itens navegará até a página de detalhes correspondente. Além disso, a página mestra também apresenta uma barra de navegação que contém um botão que pode ser usado para navegar até a página de detalhes ativa:

- No iOS, a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda.
- No Android, a barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que navega até a página de detalhes. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda, tocando a página de detalhes na extremidade direita da tela e tocando no botão *Voltar* na parte inferior da tela.
- Na UWP (Plataforma Universal do Windows), a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes.

Uma página de detalhes exibe dados correspondentes ao item selecionado na página mestra e os principais componentes da página de detalhes são mostrados nas capturas de tela a seguir:

![](master-detail-page-images/detailpage-components.png "Componentes da página de detalhes")

A página de detalhes contém uma barra de navegação, cujo conteúdo dependem da plataforma:

- No iOS, a barra de navegação está presente na parte superior da página, exibe um título e tem um botão que retorna para a página mestra, contanto que a instância da página de detalhes seja encapsulada na instância [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Além disso, é possível retornar à página mestra passando o dedo na página de detalhes para a direita.
- No Android, uma barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que retorna à página mestra. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- Na UWP, uma barra de navegação está presente na parte superior da página e exibe um título e tem botão que retorna à página mestra.

### <a name="navigation-behavior"></a>Comportamento de navegação

O comportamento da experiência de navegação entre páginas mestras e de detalhes depende da plataforma:

- No iOS, a página de detalhes *desliza* para a direita enquanto a página mestra desliza para a esquerda e a parte esquerda da página de detalhes ainda está visível.
- No Android, as páginas mestras e de detalhes são *sobrepostas* umas às outras.
- Na UWP, a página mestra desliza da esquerda para parte da página de detalhes, desde que a propriedade [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) esteja definida como `Popover`. Para obter mais informações, confira [Controlando o comportamento de exibição da página de detalhes](#Controlling_the_Detail_Page_Display_Behavior).

Um comportamento semelhante será observado no modo paisagem, exceto que a página mestra no iOS e no Android tem uma largura semelhante à da página mestra no modo retrato; portanto, mais coisas da página de detalhes ficarão visíveis.

Para saber como controlar o comportamento de navegação, confira [Controlling the Detail Page Display Behavior](#Controlling_the_Detail_Page_Display_Behavior) (Controlando o comportamento de exibição da página de detalhes).

## <a name="creating-a-masterdetailpage"></a>Criando um MasterDetailPage

Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) contém as propriedades [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) que são do tipo [`Page`](xref:Xamarin.Forms.Page), usadas para obter e definir as páginas mestras e de detalhes, respectivamente.

> [!IMPORTANT]
> Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) foi criado para ser uma página raiz e usá-la como uma página filho em outros tipos de página pode resultar em comportamento inesperado e inconsistente. Além disso, é recomendável que a página mestra de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) sempre deva ser uma instância [`ContentPage`](xref:Xamarin.Forms.ContentPage), e que a página de detalhes só deva ser populada com as instâncias [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e `ContentPage`. Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

O exemplo de código XAML a seguir mostra um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) que define as propriedades [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail):

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

O seguinte exemplo de código mostra a [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) equivalente criada em C#:

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

A propriedade [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) está definida como uma instância [`ContentPage`](xref:Xamarin.Forms.ContentPage). A propriedade [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) está definida como uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contém uma instância `ContentPage`.

### <a name="creating-the-master-page"></a>Criando a página mestra

O exemplo de código XAML a seguir mostra a declaração do objeto `MasterPage`, referenciado por meio da propriedade [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master):

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

A página consiste em uma [`ListView`](xref:Xamarin.Forms.ListView) populada com os dados em XAML definindo sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) como uma matriz de instâncias `MasterPageItem`. Cada `MasterPageItem` define propriedades `Title`, `IconSource` e `TargetType`.

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído à propriedade [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate), para exibir cada `MasterPageItem`. O `DataTemplate` contém um [`ViewCell`](xref:Xamarin.Forms.ViewCell) que consiste em um [`Image`](xref:Xamarin.Forms.Image) e em um [`Label`](xref:Xamarin.Forms.Label). O [`Image`](xref:Xamarin.Forms.Image) exibe o valor da propriedade `IconSource`, e o [`Label`](xref:Xamarin.Forms.Label) exibe o valor da propriedade `Title`, para cada `MasterPageItem`.

A página tem seu conjunto de propriedades [`Title`](xref:Xamarin.Forms.Page.Title) e [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource). O ícone será exibido na página de detalhes, desde que ela tenha uma barra de título. Isso deve ser habilitado no iOS encapsulando a instância da página de detalhes em uma instância [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

> [!NOTE]
> A página [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) deve ter a propriedade [`Title`](xref:Xamarin.Forms.Page.Title) definida ou ocorrerá uma exceção.

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

![](master-detail-page-images/masterpage.png "Exemplo de página mestra")

### <a name="creating-and-displaying-the-detail-page"></a>Criação e exibição da página de detalhes

A instância `MasterPage` contém uma propriedade `ListView` que expõe a instância [`ListView`](xref:Xamarin.Forms.ListView) para que a instância `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) possa registrar um manipulador de eventos para manipular o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected). Isso permite que a instância `MainPage` defina a propriedade [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) como a página que representa o item `ListView` selecionado. O exemplo de código a seguir mostra o manipulador de eventos:

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

- Ele recupera o [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) da instância [`ListView`](xref:Xamarin.Forms.ListView) e, contanto que não seja `null`, define a página de detalhes como uma nova instância do tipo de página armazenado na propriedade `TargetType` do `MasterPageItem`. O tipo de página é encapsulado em uma instância [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para garantir que o ícone referenciado por meio da propriedade [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) no `MasterPage` seja mostrado na página de detalhes no iOS.
- O item selecionado no [`ListView`](xref:Xamarin.Forms.ListView) é definido como `null` para garantir que nenhum dos itens `ListView` serão selecionados na próxima vez em que o `MasterPage` for apresentado.
- A página de detalhes é apresentada ao usuário definindo a propriedade [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) como `false`. Esta propriedade controla se a página mestra ou de detalhes é apresentada. Ela deve ser definida como `true` para exibir a página mestra e como `false` para exibir a página de detalhes.

As capturas de tela a seguir mostram a página de detalhes `ContactPage`, mostrada depois que ela for selecionada na página mestra:

![](master-detail-page-images/detailpage.png "Exemplo de página de detalhes")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controle do comportamento de exibição da página de detalhes

A maneira como o [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia as páginas mestras e de detalhes depende se o aplicativo está em execução em um telefone ou tablet, da orientação do dispositivo e do valor da propriedade [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior). Essa propriedade determina como a página de detalhes será exibida. Os valores possíveis são:

- **Default** – as páginas são exibidas usando o padrão da plataforma.
- **Popover** – a página de detalhes cobre ou cobre parcialmente a página mestra.
- **Split** – a página mestra é exibida à esquerda e a página de detalhes está à direita.
- **SplitOnLandscape** – Uma tela dividida é usada quando o dispositivo está na orientação paisagem.
- **SplitOnPortrait** – Uma tela dividida é usada quando o dispositivo está na orientação retrato.

O exemplo de código XAML a seguir demonstra como definir a propriedade [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) em um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

O seguinte exemplo de código mostra a [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) equivalente criada em C#:

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

Contudo, o valor da propriedade [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) afeta apenas aplicativos em execução em tablets ou na área de trabalho. Aplicativos em execução nos telefones sempre têm o comportamento *Popover*.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar uma [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e navegar entre suas páginas de informações. O `MasterDetailPage` do Xamarin.Forms é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.

## <a name="related-links"></a>Links relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
