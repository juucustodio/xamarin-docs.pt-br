---
title: Página Mestre/Detalhes do Xamarin.Forms
description: A MasterDetailPage do Xamarin.Forms é uma página que gerencia duas páginas relacionadas de informações – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar uma MasterDetailPage e navegar entre suas páginas de informações.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 702ec35bca051f7255c5c9d67d2dc68d4f89ca52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68645953"
---
# <a name="xamarinforms-master-detail-page"></a>Página Mestre/Detalhes do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_O Xamarin.Forms MasterDetailPage é uma página que gerencia duas páginas de informações relacionadas – uma página-mestre que apresenta itens e uma página de detalhes que apresenta detalhes sobre itens na página-mestre. Este artigo explica como usar um MasterDetailPage e navegar entre suas páginas de informações._

## <a name="overview"></a>Visão geral

Uma página mestra normalmente exibe uma lista de itens, conforme mostrado nas capturas de tela seguir:

[![](master-detail-page-images/masterpage-components.png "Master Page Components")](master-detail-page-images/masterpage-components-large.png#lightbox "Master Page Components")

A localização da lista de itens é idêntico em cada plataforma e selecionar um dos itens navegará até a página de detalhes correspondente. Além disso, a página mestra também apresenta uma barra de navegação que contém um botão que pode ser usado para navegar até a página de detalhes ativa:

- No iOS, a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda.
- No Android, a barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que navega até a página de detalhes. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda, tocando a página de detalhes na extremidade direita da tela e tocando no botão *Voltar* na parte inferior da tela.
- Na UWP (Plataforma Universal do Windows), a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes.

Uma página de detalhes exibe dados correspondentes ao item selecionado na página mestra e os principais componentes da página de detalhes são mostrados nas capturas de tela a seguir:

![](master-detail-page-images/detailpage-components.png "Detail Page Components")

A página de detalhes contém uma barra de navegação, cujo conteúdo dependem da plataforma:

- No iOS, a barra de navegação está presente no topo da página e exibe um título, e tem um botão [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que retorna à página-mestre, desde que a instância da página de detalhes esteja embrulhada na instância. Além disso, é possível retornar à página mestra passando o dedo na página de detalhes para a direita.
- No Android, uma barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que retorna à página mestra. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- Na UWP, uma barra de navegação está presente na parte superior da página e exibe um título e tem botão que retorna à página mestra.

### <a name="navigation-behavior"></a>Comportamento de navegação

O comportamento da experiência de navegação entre páginas mestras e de detalhes depende da plataforma:

- No iOS, a página de detalhes *desliza* para a direita enquanto a página mestra desliza para a esquerda e a parte esquerda da página de detalhes ainda está visível.
- No Android, as páginas mestras e de detalhes são *sobrepostas* umas às outras.
- No UWP, a página-mestre desliza da parte esquerda sobre parte [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) da página `Popover`de detalhes, desde que a propriedade esteja definida como . Para obter mais informações, confira [Controlando o comportamento de exibição da página de detalhes](#Controlling_the_Detail_Page_Display_Behavior).

Um comportamento semelhante será observado no modo paisagem, exceto que a página mestra no iOS e no Android tem uma largura semelhante à da página mestra no modo retrato; portanto, mais coisas da página de detalhes ficarão visíveis.

Para saber como controlar o comportamento de navegação, confira [Controlling the Detail Page Display Behavior](#Controlling_the_Detail_Page_Display_Behavior) (Controlando o comportamento de exibição da página de detalhes).

## <a name="creating-a-masterdetailpage"></a>Criando um MasterDetailPage

A [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) contém [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) e propriedades que [`Page`](xref:Xamarin.Forms.Page)são ambas de tipo, que são usadas para obter e definir as páginas mestre e de detalhes, respectivamente.

> [!IMPORTANT]
> A [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) foi projetado para ser uma página raiz, e usá-la como uma página infantil em outros tipos de página pode resultar em comportamento inesperado e inconsistente. Além disso, recomenda-se que a página [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) mestra [`ContentPage`](xref:Xamarin.Forms.ContentPage) de a deve ser sempre uma instância, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)e `ContentPage` que a página de detalhes só deve ser preenchida com [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), e instâncias. Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

O exemplo de código XAML a seguir mostra um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) que define as [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriedades: [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)

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

O exemplo de código [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) a seguir mostra o equivalente criado em C#:

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

A [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriedade está [`ContentPage`](xref:Xamarin.Forms.ContentPage) definida como uma instância. A [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade é [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) definida como `ContentPage` uma instância contendo.

### <a name="creating-the-master-page"></a>Criando a página mestra

O exemplo de código XAML `MasterPage` a seguir mostra a [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) declaração do objeto, que é referenciada através da propriedade:

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

A página consiste [`ListView`](xref:Xamarin.Forms.ListView) em uma que é preenchida com dados [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) em XAML, definindo sua propriedade para uma matriz de `MasterPageItem` instâncias. Cada `MasterPageItem` define propriedades `Title`, `IconSource` e `TargetType`.

A [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) à propriedade, `MasterPageItem`para exibir cada um . O `DataTemplate` contém [`ViewCell`](xref:Xamarin.Forms.ViewCell) um que [`Image`](xref:Xamarin.Forms.Image) consiste [`Label`](xref:Xamarin.Forms.Label)em um e um . O [`Image`](xref:Xamarin.Forms.Image) exibe `IconSource` o valor [`Label`](xref:Xamarin.Forms.Label) da `Title` propriedade, e `MasterPageItem`o exibe o valor da propriedade, para cada um .

A página [`Title`](xref:Xamarin.Forms.Page.Title) tem [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) suas propriedades definidas. O ícone será exibido na página de detalhes, desde que ela tenha uma barra de título. Isso deve ser ativado no iOS, envolvendo [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) a instância da página de detalhes em uma instância.

> [!NOTE]
> A [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) página deve [`Title`](xref:Xamarin.Forms.Page.Title) ter sua propriedade definida, ou uma exceção ocorrerá.

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

![](master-detail-page-images/masterpage.png "Master Page Example")

### <a name="creating-and-displaying-the-detail-page"></a>Criação e exibição da página de detalhes

A `MasterPage` instância `ListView` contém uma propriedade [`ListView`](xref:Xamarin.Forms.ListView) que expõe `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) sua instância para que a [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) instância possa registrar um manipulador de eventos para lidar com o evento. Isso permite `MainPage` que a [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) instância defina a propriedade `ListView` na página que representa o item selecionado. O exemplo de código a seguir mostra o manipulador de eventos:

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

- Ele recupera [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) a [`ListView`](xref:Xamarin.Forms.ListView) ocorrência e, desde que não `null`seja, define a página de detalhes para `TargetType` uma `MasterPageItem`nova instância do tipo de página armazenado na propriedade do . O tipo de página [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é embrulhado em uma instância [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) para `MasterPage` garantir que o ícone referenciado através da propriedade no é mostrado na página de detalhes no iOS.
- O item selecionado [`ListView`](xref:Xamarin.Forms.ListView) no `null` é definido para `ListView` garantir que nenhum dos itens `MasterPage` será selecionado na próxima vez que o for apresentado.
- A página de detalhes é [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) apresentada `false`ao usuário definindo a propriedade para . Esta propriedade controla se a página mestra ou de detalhes é apresentada. Ela deve ser definida como `true` para exibir a página mestra e como `false` para exibir a página de detalhes.

As capturas de tela a seguir mostram a página de detalhes `ContactPage`, mostrada depois que ela for selecionada na página mestra:

![](master-detail-page-images/detailpage.png "Detail Page Example")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controle do comportamento de exibição da página de detalhes

Como [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) o gerencia as páginas mestre e de detalhes depende se o aplicativo está sendo executado em [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) um telefone ou tablet, a orientação do dispositivo e o valor do imóvel. Essa propriedade determina como a página de detalhes será exibida. Os valores possíveis são:

- **Default** – as páginas são exibidas usando o padrão da plataforma.
- **Popover** – a página de detalhes cobre ou cobre parcialmente a página mestra.
- **Split** – a página mestra é exibida à esquerda e a página de detalhes está à direita.
- **SplitOnLandscape** – Uma tela dividida é usada quando o dispositivo está na orientação paisagem.
- **SplitOnPortrait** – Uma tela dividida é usada quando o dispositivo está na orientação retrato.

O exemplo de código XAML [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) a seguir [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)demonstra como definir a propriedade em um:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

O exemplo de código [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) a seguir mostra o equivalente criado em C#:

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

No entanto, [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) o valor da propriedade afeta apenas aplicativos em execução em tablets ou na área de trabalho. Aplicativos em execução nos telefones sempre têm o comportamento *Popover*.

## <a name="summary"></a>Resumo

Este artigo demonstrou como [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) usar um e navegar entre suas páginas de informações. O `MasterDetailPage` do Xamarin.Forms é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.

## <a name="related-links"></a>Links relacionados

- [Variedades de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
