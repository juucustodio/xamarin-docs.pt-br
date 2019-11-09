---
title: TabbedPage Xamarin. Forms
description: A TabbedPage do Xamarin.Forms consiste em uma lista de guias e uma área do detalhe maior, com cada guia carregando conteúdo na área do detalhe. Este artigo demonstra como usar uma TabbedPage para navegar por uma coleção de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 22c5b5b6479ce65c2e6b69f6ad5a98fd11ae47d7
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842906"
---
# <a name="xamarinforms-tabbedpage"></a>TabbedPage Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) do Xamarin.Forms consiste em uma lista de guias e uma área do detalhe maior, com cada guia carregando conteúdo na área do detalhe. As capturas de tela a seguir mostram uma `TabbedPage` no iOS e no Android:

[![Captura de tela de um TabbedPage contendo três guias, no iOS e no Android](tabbed-page-images/tabbedpage-today.png "TabbedPage com três guias")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage com três guias")

No iOS, a lista de guias é exibida na parte inferior da tela e a área do detalhe fica acima dela. Cada guia consiste em um título e um ícone, que deve ser um arquivo PNG com um canal alfa. Na orientação retrato, os ícones da barra de guias aparecem acima dos títulos de guias. Na orientação paisagem, os ícones e títulos aparecem lado a lado. Além disso, uma barra de guias normal ou Compact pode ser exibida, dependendo do dispositivo e da orientação. Se houver mais de cinco guias, uma guia **Mais** será exibida, que poderá ser usada para acessar as guias adicionais. Para obter informações sobre os requisitos de ícone, consulte [tamanho do ícone da barra de guias](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) em developer.Apple.com.

> [!TIP]
> O `TabbedRenderer` para iOS tem um método substituível `GetIcon` que pode ser usado para carregar ícones de guia de uma fonte especificada. Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage`. Além disso, versões selecionadas e não selecionadas de um ícone podem ser fornecidas.

No Android, a lista de guias é exibida na parte superior da tela, e a área de detalhes está abaixo. Cada guia consiste em um título e um ícone, que deve ser um arquivo PNG com um canal alfa. No entanto, as guias podem ser movidas para a parte inferior da tela com uma plataforma específica. Para obter informações sobre os requisitos de ícone, consulte [guias](https://material.io/components/tabs/#) em material.Io e [suporte a diferentes densidades de pixel](https://developer.android.com/training/multiscreen/screendensities) em developer.Android.com. Para obter informações sobre como mover as guias para a parte inferior da tela, consulte [definindo o posicionamento e a cor da barra de ferramentas TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> O `TabbedPageRenderer` para Android AppCompat tem um método `GetIconDrawable` substituível que pode ser usado para carregar ícones de guia de um `Drawable`personalizado. Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage` e funciona com barras de guia superior e inferior. Como alternativa, o método `SetTabIcon` substituível pode ser usado para carregar ícones de guia de um `Drawable` personalizado para barras de guia superior.

Na Plataforma Universal do Windows (UWP), a lista de guias é exibida na parte superior da tela e a área de detalhes está abaixo. Cada guia consiste em um título. No entanto, os ícones podem ser adicionados a cada guia com uma plataforma específica. Para obter mais informações, consulte [ícones de TabbedPage no Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Criar um TabbedPage

Duas abordagens podem ser usadas para criar uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

- Popule o [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) com uma coleção de objetos de [`Page`](xref:Xamarin.Forms.Page) filhos, como uma coleção de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Para obter mais informações, consulte [populate a TabbedPage com uma coleção de páginas](#populate-a-tabbedpage-with-a-page-collection).
- Atribua uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribua uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar páginas para objetos na coleção. Para obter mais informações, consulte [populate a TabbedPage com um modelo](#populate-a-tabbedpage-with-a-template).

Com as duas abordagens, a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) exibirá cada página conforme o usuário selecionar cada guia.

> [!IMPORTANT]
> É recomendável preencher uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) somente com as instâncias [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

Além disso, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) define as seguintes propriedades:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), do tipo [`Color`](xref:Xamarin.Forms.Color), a cor do plano de fundo da barra de guias.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), do tipo [`Color`](xref:Xamarin.Forms.Color), a cor do texto na barra de guias.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), do tipo [`Color`](xref:Xamarin.Forms.Color), a cor da guia quando ela está selecionada.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), do tipo [`Color`](xref:Xamarin.Forms.Color), a cor da guia quando ela estiver desmarcada.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que podem ser estilizadas e que as propriedades podem ser o destino de vinculações de dados.

> [!WARNING]
> Em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), cada objeto de [`Page`](xref:Xamarin.Forms.Page) é criado quando o `TabbedPage` é construído. Isso pode levar a uma experiência de usuário ruim, especialmente se a `TabbedPage` for a página raiz do aplicativo. No entanto, o Shell Xamarin. Forms permite que as páginas acessadas por meio de uma barra de guias sejam criadas sob demanda, em resposta à navegação. Para obter mais informações, confira [Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Popular um TabbedPage com uma coleção de páginas

Uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser populada com uma coleção de objetos de [`Page`](xref:Xamarin.Forms.Page) filhos, como uma coleção de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Isso é feito adicionando os objetos de `Page` à coleção de [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) . Isso é feito em XAML da seguinte maneira:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

> [!NOTE]
> A propriedade [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) da classe [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) , da qual [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva, é a `ContentProperty` de `MultiPage<T>`. Portanto, no XAML, não é necessário atribuir explicitamente os objetos [`Page`](xref:Xamarin.Forms.Page) à propriedade `Children`.

Este é o código C# equivalente:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    NavigationPage navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

Neste exemplo, a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é populada com dois objetos [`Page`](xref:Xamarin.Forms.ContentPage) . O primeiro filho é um objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) , e o segundo filho é um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contém um objeto `ContentPage`.

As capturas de tela a seguir mostram um objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

[![Captura de tela de um TabbedPage contendo três guias, no iOS e no Android](tabbed-page-images/tabbedpage-today.png "TabbedPage com três guias")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage com três guias")

Selecionar outra guia exibe o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto que representa a guia:

[![Captura de tela de um TabbedPage contendo guias, no iOS e no Android](tabbed-page-images/tabbedpage-week.png "TabbedPage com guias")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage com guias")

Na guia **agenda** , o objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) é encapsulado em um objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

> [!WARNING]
> Embora um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) possa ser colocado em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), não é recomendável colocar um `TabbedPage` em um `NavigationPage`. Isso ocorre porque, no iOS, um `UITabBarController` sempre funciona como um wrapper para o `UINavigationController`. Para obter mais informações, confira [Interfaces combinadas do controlador de exibição](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) na Biblioteca do Desenvolvedor do iOS.

## <a name="navigate-within-a-tab"></a>Navegar dentro de uma guia

A navegação pode ser executada dentro de uma guia, desde que o objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) seja encapsulado em um objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Isso é feito invocando o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) na propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) do objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

A página que está sendo navegada é especificada como o argumento para o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) . Neste exemplo, a página `UpcomingAppointmentsPage` é enviada por push para a pilha de navegação, onde se torna a página ativa:

[![Captura de tela de navegação em uma guia, no iOS e no Android](tabbed-page-images/tabbedpage-upcoming.png "Navegação TabbedPage em uma guia")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navegação TabbedPage em uma guia")

Para obter mais informações sobre como executar a navegação usando a classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), confira [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Popular um TabbedPage com um modelo

Uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser populada com páginas atribuindo uma coleção de dados à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuindo um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) que modele os dados como objetos de [`Page`](xref:Xamarin.Forms.Page) . Isso é feito em XAML da seguinte maneira:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage"
            ItemsSource="{x:Static local:MonkeyDataModel.All}">            
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

Este é o código C# equivalente:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() =>
    {
      var nameLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage
      {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children =
          {
            nameLabel,
            image,
            new StackLayout
            {
              Padding = new Thickness (50, 10),
              Children =
              {
                new StackLayout
                {
                  Orientation = StackOrientation.Horizontal,
                  Children =
                  {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                // ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

Neste exemplo, cada guia consiste em um objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) que usa [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) objetos para exibir dados para a guia:

[![Captura de tela de um modelo TabbedPage, no iOS e no Android](tabbed-page-images/tabbedpage-template.png "Modelo TabbedPage")](tabbed-page-images/tabbedpage-template-large.png#lightbox "Modelo TabbedPage")

Selecionar outra guia exibe o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto que representa a guia.

## <a name="related-links"></a>Links relacionados

- [TabbedPageWithNavigationPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API TabbedPage](xref:Xamarin.Forms.TabbedPage)
