---
title: Xamarin.FormsTabbedPage
description: O Xamarin.Forms TabbedPage consiste em uma lista de guias e uma área de detalhes maior, com cada guia carregando conteúdo na área de detalhes. Este artigo demonstra como usar uma TabbedPage para navegar por uma coleção de páginas.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38389867ba52e63d8310e3b59d7838f58e8cf488
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137508"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.FormsTabbedPage

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

O Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) consiste em uma lista de guias e uma área de detalhes maior, com cada guia carregando conteúdo na área de detalhes. As capturas de tela a seguir mostram um `TabbedPage` no Ios e no Android:

[![Captura de tela de um TabbedPage contendo três guias, no iOS e no Android](tabbed-page-images/tabbedpage-today.png "TabbedPage com três guias")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage com três guias")

No iOS, a lista de guias é exibida na parte inferior da tela e a área do detalhe fica acima dela. Cada guia consiste em um título e um ícone, que deve ser um arquivo PNG com um canal alfa. Na orientação retrato, os ícones da barra de guias aparecem acima dos títulos de guias. Na orientação paisagem, os ícones e títulos aparecem lado a lado. Além disso, uma barra de guias normal ou Compact pode ser exibida, dependendo do dispositivo e da orientação. Se houver mais de cinco guias, uma guia **Mais** será exibida, que poderá ser usada para acessar as guias adicionais. Para obter informações sobre os requisitos de ícone, consulte [tamanho do ícone da barra de guias](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) em developer.Apple.com.

> [!TIP]
> O `TabbedRenderer` para IOS tem um método substituível `GetIcon` que pode ser usado para carregar ícones de guia de uma fonte especificada. Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage`. Além disso, versões selecionadas e não selecionadas de um ícone podem ser fornecidas.

No Android, a lista de guias é exibida na parte superior da tela, e a área de detalhes está abaixo. Cada guia consiste em um título e um ícone, que deve ser um arquivo PNG com um canal alfa. No entanto, as guias podem ser movidas para a parte inferior da tela com uma plataforma específica. Se houver mais de cinco guias e a lista de guias estiver na parte inferior da tela, será exibida uma guia *mais* que pode ser usada para acessar as guias adicionais. Para obter informações sobre os requisitos de ícone, consulte [guias](https://material.io/components/tabs/#) em material.Io e [suporte a diferentes densidades de pixel](https://developer.android.com/training/multiscreen/screendensities) em developer.Android.com. Para obter informações sobre como mover as guias para a parte inferior da tela, consulte [definindo o posicionamento e a cor da barra de ferramentas TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> O `TabbedPageRenderer` para Android AppCompat tem um método substituível `GetIconDrawable` que pode ser usado para carregar ícones de guia de um personalizado `Drawable` . Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage` e funciona com barras de guia superior e inferior. Como alternativa, o método `SetTabIcon` substituível pode ser usado para carregar ícones de guia de um `Drawable` personalizado para barras de guia superior.

Na Plataforma Universal do Windows (UWP), a lista de guias é exibida na parte superior da tela e a área de detalhes está abaixo. Cada guia consiste em um título. No entanto, os ícones podem ser adicionados a cada guia com uma plataforma específica. Para obter mais informações, consulte [ícones de TabbedPage no Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Criar uma TabbedPage

Duas abordagens podem ser usadas para criar um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

- Popule o [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) com uma coleção de [`Page`](xref:Xamarin.Forms.Page) objetos filho, como uma coleção de [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos. Para obter mais informações, consulte [populate a TabbedPage com uma coleção de páginas](#populate-a-tabbedpage-with-a-page-collection).
- Atribuir uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar páginas para objetos na coleção. Para obter mais informações, consulte [populate a TabbedPage com um modelo](#populate-a-tabbedpage-with-a-template).

Com ambas as abordagens, o [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) exibirá cada página à medida que o usuário selecionar cada guia.

> [!IMPORTANT]
> É recomendável que um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deva ser populado [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) [`ContentPage`](xref:Xamarin.Forms.ContentPage) apenas com instâncias e. Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

Além disso, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) o define as seguintes propriedades:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor do plano de fundo da barra de guias.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor do texto na barra de guias.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor da guia quando ela é selecionada.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor da guia quando ela está desmarcada.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser os destinos das associações de dados.

> [!WARNING]
> Em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , cada [`Page`](xref:Xamarin.Forms.Page) objeto é criado quando o `TabbedPage` é construído. Isso pode levar a uma experiência de usuário ruim, especialmente se a `TabbedPage` for a página raiz do aplicativo. No entanto, Xamarin.Forms o Shell permite que as páginas acessadas por meio de uma barra de guias sejam criadas sob demanda, em resposta à navegação. Para obter mais informações, consulte [ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Popular um TabbedPage com uma coleção de páginas

Um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser preenchido com uma coleção de [`Page`](xref:Xamarin.Forms.Page) objetos filho, como uma coleção de [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos. Isso é obtido com a adição dos `Page` objetos à [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) coleção. Isso é feito em XAML da seguinte maneira:

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
> A [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) propriedade da [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) classe, da qual [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva, é o `ContentProperty` de `MultiPage<T>` . Portanto, no XAML, não é necessário atribuir explicitamente os [`Page`](xref:Xamarin.Forms.Page) objetos à `Children` propriedade.

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

Neste exemplo, o [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é preenchido com dois [`Page`](xref:Xamarin.Forms.ContentPage) objetos. O primeiro filho é um [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto, e o segundo filho é um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objeto que o contém `ContentPage` .

As capturas de tela a seguir mostram um [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

[![Captura de tela de um TabbedPage contendo três guias, no iOS e no Android](tabbed-page-images/tabbedpage-today.png "TabbedPage com três guias")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage com três guias")

Selecionar outra guia exibe o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto que representa a guia:

[![Captura de tela de um TabbedPage contendo guias, no iOS e no Android](tabbed-page-images/tabbedpage-week.png "TabbedPage com guias")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage com guias")

Na guia **agenda** , o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto é encapsulado em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objeto.

> [!WARNING]
> Embora um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) possa ser colocado em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , não é recomendável colocar um `TabbedPage` em um `NavigationPage` . Isso ocorre porque, no iOS, um `UITabBarController` sempre funciona como um wrapper para o `UINavigationController`. Para obter mais informações, confira [Interfaces combinadas do controlador de exibição](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) na Biblioteca do Desenvolvedor do iOS.

## <a name="navigate-within-a-tab"></a>Navegar dentro de uma guia

A navegação pode ser executada dentro de uma guia, desde que o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto seja encapsulado em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objeto. Isso é feito invocando o [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) método na [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) Propriedade do [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto:

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

A página que está sendo navegada é especificada como o argumento para o [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) método. Neste exemplo, a `UpcomingAppointmentsPage` página é empurrada para a pilha de navegação, onde se torna a página ativa:

[![Captura de tela de navegação em uma guia, no iOS e no Android](tabbed-page-images/tabbedpage-upcoming.png "Navegação TabbedPage em uma guia")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navegação TabbedPage em uma guia")

Para obter mais informações sobre como executar a navegação usando a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe, consulte [navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Popular um TabbedPage com um modelo

Um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser preenchido com páginas atribuindo uma coleção de dados à [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade e atribuindo um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade que modele os dados como [`Page`](xref:Xamarin.Forms.Page) objetos. Isso é feito em XAML da seguinte maneira:

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

Neste exemplo, cada guia consiste em um [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto que usa [`Image`](xref:Xamarin.Forms.Image) objetos e [`Label`](xref:Xamarin.Forms.Label) para exibir dados para a guia:

[![Captura de tela de um modelo TabbedPage, no iOS e no Android](tabbed-page-images/tabbedpage-template.png "Modelo TabbedPage")](tabbed-page-images/tabbedpage-template-large.png#lightbox "Modelo TabbedPage")

Selecionar outra guia exibe o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto que representa a guia.

## <a name="related-links"></a>Links relacionados

- [TabbedPageWithNavigationPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Variedades de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API TabbedPage](xref:Xamarin.Forms.TabbedPage)
