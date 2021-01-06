---
title: Xamarin.Forms FlyoutPage
description: O Xamarin.Forms FlyoutPage é uma página que gerencia duas páginas de informações relacionadas – uma página de submenu que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página do submenu. Este artigo explica como usar um FlyoutPage e navegar entre suas páginas de informações.
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ad3b5d76816710d45cd1d8e738771e39555401fa
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940656"
---
# <a name="no-locxamarinforms-flyoutpage"></a>Xamarin.Forms FlyoutPage

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)

Uma página de submenu normalmente exibe uma lista de itens, conforme mostrado nas seguintes capturas de tela:

[![Componentes da página do submenu](flyoutpage-images/flyoutpage-components.png)](flyoutpage-images/flyoutpage-components-large.png#lightbox "Componentes da página do submenu")

A localização da lista de itens é idêntico em cada plataforma e selecionar um dos itens navegará até a página de detalhes correspondente. Além disso, a página do submenu também apresenta uma barra de navegação que contém um botão que pode ser usado para navegar até a página de detalhes ativa:

- No iOS, a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes. Além disso, é possível navegar até a página de detalhes ativa passando o dedo na página mestra para a esquerda.
- No Android, a barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que navega até a página de detalhes. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android. Além disso, a página de detalhes ativa pode ser navegada passando o dedo para a esquerda da página do submenu, tocando na página de detalhes na extrema direita da tela e tocando no botão *voltar* na parte inferior da tela.
- Na UWP (Plataforma Universal do Windows), a barra de navegação está presente na parte superior da página e tem um botão que navega até a página de detalhes.

Uma página de detalhes exibe dados que correspondem ao item selecionado na página do submenu e os principais componentes da página de detalhes são mostrados nas seguintes capturas de tela:

![Componentes da página de detalhes](flyoutpage-images/detailpage-components.png)

A página de detalhes contém uma barra de navegação, cujo conteúdo dependem da plataforma:

- No iOS, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna à página do submenu, desde que a instância da página de detalhes seja encapsulada na [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância. Além disso, a página do submenu pode ser retornada ao passar a página de detalhes para a direita.
- No Android, uma barra de navegação está presente na parte superior da página e exibe um título, um ícone e um botão que retorna à página do submenu. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- No UWP, a barra de navegação está presente na parte superior da página e exibe um título e tem um botão que retorna à página do submenu.

## <a name="navigation-behavior"></a>Comportamento de navegação

O comportamento da experiência de navegação entre as páginas de submenu e de detalhes é dependente da plataforma:

- No iOS, a página de detalhes *desliza* para a direita à medida que a página do submenu é exibida da esquerda e a parte esquerda da página de detalhes ainda está visível.
- No Android, as páginas de detalhes e de submenu são *sobrepostas* umas nas outras.
- No UWP, a página do submenu desliza da esquerda sobre parte da página de detalhes, desde que a [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) Propriedade esteja definida como `Popover` .

O comportamento semelhante será observado no modo paisagem, exceto que a página do submenu no iOS e no Android tem uma largura semelhante à da página do submenu no modo retrato, de modo que mais da página de detalhes estará visível.

Para obter informações sobre como controlar o comportamento de navegação, consulte [controlar o comportamento do layout da página de detalhes](#control-the-detail-page-layout-behavior).

## <a name="create-a-flyoutpage"></a>Criar um FlyoutPage

Um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) Contains [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) e [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) Propriedades que são do tipo [`Page`](xref:Xamarin.Forms.Page) , que são usadas para obter e definir as páginas de submenu e detalhes respectivamente.

> [!IMPORTANT]
> Um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) é projetado para ser uma página raiz e usá-lo como uma página filho em outros tipos de página pode resultar em comportamento inesperado e inconsistente. Além disso, é recomendável que a página de submenu de a `FlyoutPage` sempre seja uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) instância e que a página de detalhes só deva ser populada com [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instâncias, e `ContentPage` . Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

O exemplo de código XAML a seguir mostra um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) que define as [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) Propriedades e:

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:FlyoutPageNavigation;assembly=FlyoutPageNavigation"
            x:Class="FlyoutPageNavigation.MainPage">
    <FlyoutPage.Flyout>
        <local:FlyoutMenuPage x:Name="flyoutPage" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </FlyoutPage.Detail>
</FlyoutPage>
```

O exemplo de código a seguir mostra o equivalente [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) criado em C#:

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        flyoutPage = new FlyoutMenuPageCS();
        Flyout = flyoutPage;
        Detail = new NavigationPage(new ContactsPageCS());
        ...
    }
    ...
}    
```

A [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) propriedade é definida como uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) instância. A [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) propriedade é definida como um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contém uma `ContentPage` instância.

### <a name="create-the-flyout-page"></a>Criar a página do submenu

O exemplo de código XAML a seguir mostra a declaração do `FlyoutMenuPage` objeto, que é referenciada por meio da [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) Propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:FlyoutPageNavigation"
             x:Class="FlyoutPageNavigation.FlyoutMenuPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:FlyoutPageItem}">
                    <local:FlyoutPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:FlyoutPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:FlyoutPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
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

A página consiste em uma [`ListView`](xref:Xamarin.Forms.ListView) que é preenchida com dados em XAML, definindo sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade como uma matriz de `FlyoutPageItem` objetos. Cada `FlyoutPageItem` define propriedades `Title`, `IconSource` e `TargetType`.

Um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) é atribuído à [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade, para exibir cada `FlyoutPageItem` . O `DataTemplate` contém um [`ViewCell`](xref:Xamarin.Forms.ViewCell) que consiste em um [`Image`](xref:Xamarin.Forms.Image) e um [`Label`](xref:Xamarin.Forms.Label) . O [`Image`](xref:Xamarin.Forms.Image) exibe o `IconSource` valor da propriedade e [`Label`](xref:Xamarin.Forms.Label) exibe o `Title` valor da propriedade para cada um `FlyoutPageItem` .

A página tem suas [`Title`](xref:Xamarin.Forms.Page.Title) [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) Propriedades e definidas. O ícone será exibido na página de detalhes, desde que ela tenha uma barra de título. Isso deve ser habilitado no iOS encapsulando a instância da página de detalhes em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância.

> [!NOTE]
> A [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) página deve ter sua [`Title`](xref:Xamarin.Forms.Page.Title) propriedade definida ou ocorrerá uma exceção.

O exemplo de código a seguir mostra a página equivalente criada em C#:

```csharp
public class FlyoutMenuPageCS : ContentPage
{
    ListView listView;
    public ListView ListView { get { return listView; } }

    public FlyoutMenuPageCS()
    {
        var flyoutPageItems = new List<FlyoutPageItem>();
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Contacts",
            IconSource = "contacts.png",
            TargetType = typeof(ContactsPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "TodoList",
            IconSource = "todo.png",
            TargetType = typeof(TodoListPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Reminders",
            IconSource = "reminders.png",
            TargetType = typeof(ReminderPageCS)
        });

        listView = new ListView
        {
            ItemsSource = flyoutPageItems,
            ItemTemplate = new DataTemplate(() =>
            {
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
        Padding = new Thickness(0, 40, 0, 0);
        Content = new StackLayout
        {
            Children = { listView }
        };
    }
}
```

As capturas de tela a seguir mostram a página do submenu em cada plataforma:

![Exemplo de página mestra](flyoutpage-images/flyoutpage.png)

### <a name="create-and-display-the-detail-page"></a>Criar e exibir a página de detalhes

A `FlyoutMenuPage` instância contém uma `ListView` propriedade que expõe sua [`ListView`](xref:Xamarin.Forms.ListView) instância para que a `MainPage` [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) instância possa registrar um manipulador de eventos para manipular o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento. Isso permite que a `MainPage` instância defina a [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) propriedade para a página que representa o `ListView` item selecionado. O exemplo de código a seguir mostra o manipulador de eventos:

```csharp
public partial class MainPage : FlyoutPage
{
    public MainPage()
    {
        ...
        flyoutPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as FlyoutPageItem;
        if (item != null)
        {
            Detail = new NavigationPage((Page)Activator.CreateInstance(item.TargetType));
            flyoutPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

O método `OnItemSelected` executa as seguintes ações:

- Ele recupera o [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) da [`ListView`](xref:Xamarin.Forms.ListView) instância do e, desde que não esteja `null` , define a página de detalhes como uma nova instância do tipo de página armazenada na `TargetType` Propriedade do `FlyoutPageItem` . O tipo de página é encapsulado em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância para garantir que o ícone referenciado por meio da [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propriedade no `FlyoutMenuPage` é mostrado na página de detalhes no Ios.
- O item selecionado no [`ListView`](xref:Xamarin.Forms.ListView) é definido como `null` para garantir que nenhum dos itens seja `ListView` selecionado na próxima vez em que `FlyoutMenuPage` for apresentado.
- A página de detalhes é apresentada ao usuário definindo a [`FlyoutPage.IsPresented`](xref:Xamarin.Forms.FlyoutPage.IsPresented) propriedade como `false` . Essa propriedade controla se a página de submenu ou de detalhes é apresentada. Ele deve ser definido como `true` para exibir a página do submenu e para `false` exibir a página de detalhes.

As capturas de tela a seguir mostram a `ContactPage` página de detalhes, que é mostrada após sua seleção na página do submenu:

![Exemplo de página de detalhes](flyoutpage-images/detailpage.png)

### <a name="control-the-detail-page-layout-behavior"></a>Controlar o comportamento do layout da página de detalhes

Como o [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) gerencia as páginas de submenu e de detalhes depende se o aplicativo está sendo executado em um telefone ou Tablet, a orientação do dispositivo e o valor da [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriedade. Essa propriedade determina como a página de detalhes será exibida. Os valores possíveis são:

- `Default` – As páginas são exibidas usando o padrão de plataforma.
- `Popover` – A página de detalhes cobre ou aborda parcialmente a página do submenu.
- `Split` – A página do submenu é exibida à esquerda e a página de detalhes está à direita.
- `SplitOnLandscape` – Uma tela dividida é usada quando o dispositivo está na orientação paisagem.
- `SplitOnPortrait` – Uma tela de divisão é usada quando o dispositivo está na orientação retrato.

O exemplo de código XAML a seguir demonstra como definir a [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriedade em um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) :

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            x:Class="FlyoutPageNavigation.MainPage"
            FlyoutLayoutBehavior="Popover">
  ...
</FlyoutPage>
```

O exemplo de código a seguir mostra o equivalente [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) criado em C#:

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        ...
        FlyoutLayoutBehavior = FlyoutLayoutBehavior.Popover;
    }
}
```

> [!IMPORTANT]
> O valor da [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) propriedade afeta apenas os aplicativos em execução nos tablets ou na área de trabalho. Os aplicativos em execução em telefones sempre têm o `Popover` comportamento.

## <a name="related-links"></a>Links relacionados

- [Variedades de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [FlyoutPage (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)
- [API FlyoutPage](xref:Xamarin.Forms.FlyoutPage)
