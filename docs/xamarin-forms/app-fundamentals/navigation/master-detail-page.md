---
title: Página de detalhes mestre
description: O MasterDetailPage xamarin. Forms é uma página que gerencia as duas páginas relacionadas de informações – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar um MasterDetailPage e navegar entre as páginas de informações.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e1df494edfc82f3a168e56553db6f43e63217def
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="master-detail-page"></a>Página de detalhes mestre

_O MasterDetailPage xamarin. Forms é uma página que gerencia as duas páginas relacionadas de informações – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra. Este artigo explica como usar um MasterDetailPage e navegar entre as páginas de informações._

## <a name="overview"></a>Visão geral

Uma página mestra exibe uma lista de itens, como mostrado nas capturas de tela seguir:

[![](master-detail-page-images/masterpage-components.png "Componentes de página mestra")](master-detail-page-images/masterpage-components-large.png#lightbox "componentes de página mestra")

O local da lista de itens é idêntico em cada plataforma e selecionar um dos itens navegará para a página de detalhes correspondentes. Além disso, a página mestra também apresenta uma barra de navegação que contém um botão que pode ser usado para navegar até a página de detalhes de ativo:

- No iOS, a barra de navegação está presente na parte superior da página e tem um botão que navega para a página de detalhes. Além disso, a página de detalhes do ativo pode ser acessada quando a página mestra para a esquerda.
- No Android, a barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que navega para a página de detalhes. O ícone é definido no `[Activity]` atributo decora o `MainActivity` classe no projeto específico da plataforma Android. Além disso, a página de detalhes do ativo pode ser acessada quando a página mestra à esquerda, tocando a página de detalhes na extremidade direita da tela e tocando a *novamente* botão na parte inferior da tela.
- Sobre o Windows UWP (plataforma Universal), barra de navegação está presente na parte superior da página e tem um botão que navega para a página de detalhes.

Detalhes página exibe dados que corresponde ao item selecionado no mestre de página, e os componentes principais da página de detalhes são mostrados nas capturas de tela seguir:

![](master-detail-page-images/detailpage-components.png "Componentes de página de detalhes")

A página de detalhes contém uma barra de navegação, cujo conteúdo é dependente de plataforma:

- No iOS, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna para a página mestra, desde que a instância de página de detalhes é encapsulada no [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância. Além disso, a página mestra pode ser retornada para quando a página de detalhes à direita.
- No Android, uma barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que retorna para a página mestra. O ícone é definido no `[Activity]` atributo decora o `MainActivity` classe no projeto específico da plataforma Android.
- Em UWP, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna para a página mestra.

### <a name="navigation-behavior"></a>Comportamento de navegação

O comportamento da experiência de navegação entre páginas mestre e de detalhes é dependente de plataforma:

- No iOS, a página de detalhes *slides* à direita como o trilho de página mestra, e a parte esquerda do detalhe página ainda está visível.
- No Android, as páginas de detalhes e mestre são *sobreposta* uns dos outros.
- Em UWP, as páginas de detalhes e mestre são *trocado*.

Comportamento semelhante será observado no modo paisagem, exceto que a página mestra no iOS e Android tem uma largura de semelhante como a página mestra no modo retrato, então, mais a página de detalhes será visível.

Para obter informações sobre como controlar o comportamento de navegação, consulte [controlar o comportamento de exibição de página de detalhes](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Criando um MasterDetailPage

Um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) contém [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) propriedades que são do tipo [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), que são usados para obter e definir as páginas mestre e de detalhes respectivamente.

> [!IMPORTANT]
> Um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) foi projetado para ser uma página de raiz e usá-lo como uma página filho em outros tipos de página pode resultar em comportamento inesperado e inconsistente. Além disso, recomenda-se que a página mestra de um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) sempre deve ser um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância e que a página de detalhes só deve ser populada com [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), e `ContentPage` instâncias. Isso ajudará a garantir uma experiência de usuário consistente em todas as plataformas.

O exemplo do código XAML seguinte mostra um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) que define o [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) propriedades:

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

O exemplo de código a seguir mostra o equivalente [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) criado em c#:

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

O [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) está definida como um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância. O [ `MasterDetailPage.Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) está definida como um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) que contém um `ContentPage` instância.

### <a name="creating-the-master-page"></a>Criando a página mestra

O exemplo de código XAML a seguir mostra a declaração do `MasterPage` objeto, que é referenciado por meio de [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView">
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

A página consiste em uma [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que é preenchida com dados em XAML, definindo seu [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propriedade para uma matriz de `MasterPageItem` instâncias. Cada `MasterPageItem` define `Title`, `IconSource`, e `TargetType` propriedades.

Um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) é atribuído para o [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propriedade para exibir cada `MasterPageItem`. O `DataTemplate` contém um [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) que consiste em uma [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/). O [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) exibe o `IconSource` valor da propriedade e o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) exibe o `Title` valor da propriedade, para cada `MasterPageItem`.

A página tem seu [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) e [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) conjunto de propriedades. O ícone será exibido na página de detalhes, desde que a página de detalhes possui uma barra de título. Isso deve ser habilitado no iOS encapsulando a instância de página de detalhes em uma [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância.

> [!NOTE]
> O [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) página deve ter seu [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) propriedade definida ou ocorrerá uma exceção.

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

O `MasterPage` instância contém um `ListView` propriedade expõe seu [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) instância para que o `MainPage` [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) instância pode registrar um manipulador de eventos para lidar com o [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) eventos. Isso permite que o `MainPage` instância para definir o [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) propriedade para a página que representa o selecionado `ListView` item. O exemplo de código a seguir mostra o manipulador de eventos:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.ListView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.ListView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

O `OnItemSelected` método executa as seguintes ações:

- Recupera o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) do [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) instância e contanto que não esteja `null`, define a página de detalhes para uma nova instância do tipo de página armazenado na `TargetType`propriedade o `MasterPageItem`. O tipo de página é encapsulado em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância para garantir que o ícone referenciado por meio de [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) propriedade no `MasterPage` é mostrado na página de detalhes no iOS.
- O item selecionado no [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é definido como `null` para garantir que nenhum do `ListView` itens serão selecionados a próxima vez que o `MasterPage` é apresentado.
- A página de detalhes é apresentada ao usuário definindo o [ `MasterDetailPage.IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) propriedade `false`. Essa propriedade controla se a página mestra ou de detalhes é apresentada. Ele deve ser definido como `true` para exibir a página mestre e `false` para exibir a página de detalhes.

O capturas de tela a seguir mostram o `ContactPage` página de detalhes, que é exibida depois que ela foi selecionada na página mestre:

![](master-detail-page-images/detailpage.png "Exemplo de página de detalhes")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controlar o comportamento de exibição de página de detalhes

Como o [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) gerencia as páginas mestre e de detalhes depende se o aplicativo está em execução em um telefone ou tablet, a orientação do dispositivo e o valor da [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) propriedade. Essa propriedade determina como a página de detalhes será exibida. Ele os valores possíveis são:

- **Padrão** – as páginas são exibidas usando o padrão de plataforma.
- **Popover** – a página de detalhes abrange ou abrange parcialmente a página mestra.
- **Divisão** – a página mestra é exibida à esquerda e a página de detalhes está à direita.
- **SplitOnLandscape** – uma tela dividida é usada quando o dispositivo estiver na orientação paisagem.
- **SplitOnPortrait** – uma tela dividida é usada quando o dispositivo estiver na orientação retrato.

O exemplo de código XAML a seguir demonstra como definir a [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) propriedade em uma [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

O exemplo de código a seguir mostra o equivalente [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) criado em c#:

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

No entanto, o valor de [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) propriedade afeta apenas a aplicativos executados em tablets ou a área de trabalho. Aplicativos em execução em telefones sempre tem o *Popover* comportamento.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) e navegue entre as páginas de informações. O xamarin. Forms `MasterDetailPage` é uma página que gerencia as duas páginas de informações relacionadas a uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)
