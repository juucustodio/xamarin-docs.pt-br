---
title: Página Master-Detail do xamarin. Forms
description: MasterDetailPage o xamarin. Forms é uma página que gerencia duas páginas relacionadas de informações – uma página mestra que apresenta os itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar um MasterDetailPage e navegar entre suas páginas de informações.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: a3d0edbd933339ee8b8a0a277a4f2493cc8dc70e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997459"
---
# <a name="xamarinforms-master-detail-page"></a>Página Master-Detail do xamarin. Forms

_MasterDetailPage o xamarin. Forms é uma página que gerencia duas páginas relacionadas de informações – uma página mestra que apresenta os itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar um MasterDetailPage e navegar entre suas páginas de informações._

## <a name="overview"></a>Visão geral

Uma página mestra normalmente exibe uma lista de itens, conforme mostrado nas capturas de tela seguir:

[![](master-detail-page-images/masterpage-components.png "Componentes da página mestra")](master-detail-page-images/masterpage-components-large.png#lightbox "componentes da página mestra")

O local da lista de itens é idêntico em cada plataforma e selecionar um dos itens navegará para a página de detalhes correspondente. Além disso, a página mestra também apresenta uma barra de navegação que contém um botão que pode ser usado para navegar até a página de detalhes de ativo:

- No iOS, a barra de navegação está presente na parte superior da página e tem um botão que navega para a página de detalhes. Além disso, a página de detalhes do Active Directory pode ser navegada passando o dedo para a página mestra para a esquerda.
- No Android, a barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que navega para a página de detalhes. O ícone é definido na `[Activity]` atributo decora o `MainActivity` classe no projeto específico da plataforma Android. Além disso, a página de detalhes do Active Directory pode ser navegada passando o dedo para a página mestra para a esquerda, ao tocar a página de detalhes na extremidade direita da tela e tocando o *volta* botão na parte inferior da tela.
- Na Universal Windows Platform (UWP), a barra de navegação está presente na parte superior da página e tem um botão que navega para a página de detalhes.

Um detalhe página exibe os dados que corresponde ao item selecionado no mestre de página e os principais componentes da página de detalhes são mostrados nas seguintes capturas de tela:

![](master-detail-page-images/detailpage-components.png "Componentes da página de detalhes")

A página de detalhes contém uma barra de navegação, cujo conteúdo é dependente de plataforma:

- No iOS, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna para a página mestra, desde que a instância de página de detalhes é encapsulada na [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância. Além disso, a página mestra pode ser retornada para passando o dedo para a página de detalhes à direita.
- No Android, uma barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que retorna à página mestra. O ícone é definido na `[Activity]` atributo decora o `MainActivity` classe no projeto específico da plataforma Android.
- Na UWP, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna à página mestra.

### <a name="navigation-behavior"></a>Comportamento de navegação

O comportamento da experiência de navegação entre páginas mestre e de detalhes é dependente da plataforma:

- No iOS, a página de detalhes *slides* à direita como os slides da página mestra da esquerda e a parte esquerda do detalhe página ainda está visível.
- No Android, as páginas de detalhes e mestre estão *sobrepostos* uns nos outros.
- Na UWP, as páginas de detalhes e mestre estão *trocado*.

Um comportamento semelhante será observado no modo paisagem, exceto que a página mestra no iOS e Android tem uma largura de semelhante como a página mestra no modo retrato, portanto, mais a página de detalhes ficará visível.

Para obter informações sobre como controlar o comportamento de navegação, consulte [controlando o comportamento de exibição de página de detalhes](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Criando um MasterDetailPage

Um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) contém [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedades que são ambos do tipo [ `Page` ](xref:Xamarin.Forms.Page), que são usados para obter e definir as páginas mestre e de detalhes, respectivamente.

> [!IMPORTANT]
> Um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) foi projetado para ser uma página de raiz e usá-lo como uma página filho em outros tipos de página pode resultar em comportamento inesperado e inconsistente. Além disso, é recomendável que a página mestra de um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) sempre deve ser um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância, e que a página de detalhes só deve ser preenchida com [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), e `ContentPage` instâncias. Isso ajudará a garantir uma experiência de usuário consistente em todas as plataformas.

Mostra o exemplo de código XAML abaixo uma [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) que define os [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedades:

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

O exemplo de código a seguir mostra o equivalente [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) criado em c#:

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

O [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) estiver definida como um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância. O [ `MasterDetailPage.Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) estiver definida como um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que contém um `ContentPage` instância.

### <a name="creating-the-master-page"></a>Criando a página mestra

O exemplo de código XAML a seguir mostra a declaração do `MasterPage` objeto, que é referenciado por meio de [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
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

A página consiste em uma [ `ListView` ](xref:Xamarin.Forms.ListView) que é preenchido com dados em XAML, definindo seu [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade para uma matriz de `MasterPageItem` instâncias. Cada `MasterPageItem` define `Title`, `IconSource`, e `TargetType` propriedades.

Um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) é atribuído para o [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade, para exibir cada `MasterPageItem`. O `DataTemplate` contém uma [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) que consiste em um [ `Image` ](xref:Xamarin.Forms.Image) e um [ `Label` ](xref:Xamarin.Forms.Label). O [ `Image` ](xref:Xamarin.Forms.Image) exibe os `IconSource` valor da propriedade e o [ `Label` ](xref:Xamarin.Forms.Label) exibe o `Title` valor da propriedade, para cada `MasterPageItem`.

A página tem seu [ `Title` ](xref:Xamarin.Forms.Page.Title) e [ `Icon` ](xref:Xamarin.Forms.Page.Icon) conjunto de propriedades. O ícone será exibido na página de detalhes, desde que a página de detalhes tem uma barra de título. Isso deve ser habilitado no iOS, encapsulando a instância de página de detalhes em uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância.

> [!NOTE]
> O [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) página deverá ter sua [ `Title` ](xref:Xamarin.Forms.Page.Title) propriedade definida, ou ocorrerá uma exceção.

O exemplo de código a seguir mostra a página equivalente criada em c#:

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

    Icon = "hamburger.png";
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

### <a name="creating-and-displaying-the-detail-page"></a>Criando e exibindo a página de detalhes

O `MasterPage` instância contém uma `ListView` propriedade que expõe seus [ `ListView` ](xref:Xamarin.Forms.ListView) instância para que o `MainPage` [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) instância pode registrar um manipulador de eventos para lidar com o [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventos. Isso permite que o `MainPage` instância para definir o [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade para a página que representa o selecionado `ListView` item. O exemplo de código a seguir mostra o manipulador de eventos:

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

O `OnItemSelected` método executa as seguintes ações:

- Ele recupera o [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) da [ `ListView` ](xref:Xamarin.Forms.ListView) da instância e fornecido que não é `null`, define a página de detalhes para uma nova instância do tipo de página armazenada em do `TargetType`propriedade do `MasterPageItem`. O tipo de página é encapsulado em uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância para garantir que o ícone referenciado por meio de [ `Icon` ](xref:Xamarin.Forms.Page.Icon) propriedade no `MasterPage` é mostrado na página de detalhes no iOS.
- O item selecionado na [ `ListView` ](xref:Xamarin.Forms.ListView) é definido como `null` para garantir que nenhum dos `ListView` itens serão selecionados a próxima vez em que o `MasterPage` é apresentado.
- A página de detalhes é apresentada ao usuário definindo o [ `MasterDetailPage.IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriedade `false`. Esta propriedade controla se a página mestra ou de detalhes é apresentada. Ele deve ser definido como `true` para exibir a página mestra e, ao `false` para exibir a página de detalhes.

As capturas de tela a seguir mostram o `ContactPage` página de detalhes, que é mostrada depois que ela foi selecionada na página mestra:

![](master-detail-page-images/detailpage.png "Exemplo de página de detalhes")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controlando o comportamento de exibição de página de detalhes

Como o [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) gerencia as páginas mestre e de detalhes depende se o aplicativo está em execução em um telefone ou tablet, a orientação do dispositivo e o valor da [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade. Essa propriedade determina como a página de detalhes será exibida. Seus valores possíveis são:

- **Padrão** – as páginas são exibidas usando o padrão de plataforma.
- **Pop-over** – a página de detalhes aborda ou cobre parcialmente a página mestra.
- **Divisão** – a página mestra é exibida à esquerda e a página de detalhes está à direita.
- **SplitOnLandscape** – uma tela dividida é usada quando o dispositivo estiver na orientação paisagem.
- **SplitOnPortrait** – uma tela dividida é usada quando o dispositivo estiver na orientação retrato.

O exemplo de código XAML a seguir demonstra como definir a [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade em uma [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

O exemplo de código a seguir mostra o equivalente [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) criado em c#:

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

No entanto, o valor de [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade afeta apenas aplicativos executados em tablets ou a área de trabalho. Aplicativos em execução nos telefones sempre têm o *pop-over* comportamento.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) e navegar entre suas páginas de informações. O xamarin. Forms `MasterDetailPage` é uma página que gerencia duas páginas de informações relacionadas a uma página mestra que apresenta os itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
