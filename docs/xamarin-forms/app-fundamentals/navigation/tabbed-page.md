---
title: Xamarin.Forms TabbedPage
description: A TabbedPage do Xamarin.Forms consiste em uma lista de guias e uma área do detalhe maior, com cada guia carregando conteúdo na área do detalhe. Este artigo demonstra como usar uma TabbedPage para navegar por uma coleção de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305083"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.Forms TabbedPage

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

O Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) consiste em uma lista de guias e uma área de detalhes maior, com cada conteúdo de carregamento de guias na área de detalhes. As capturas de `TabbedPage` tela a seguir mostram um no iOS e Android:

[![Captura de tela de uma TabbedPage contendo três guias, no iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage com três guias")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage com três guias")

No iOS, a lista de guias é exibida na parte inferior da tela e a área do detalhe fica acima dela. Cada guia consiste em um título e um ícone, que deve ser um arquivo PNG com um canal alfa. Na orientação do retrato, os ícones da barra de guia saem acima dos títulos da guia. Na orientação da paisagem, ícones e títulos aparecem lado a lado. Além disso, uma barra de guia regular ou compacta pode ser exibida, dependendo do dispositivo e da orientação. Se houver mais de cinco guias, uma guia **Mais** será exibida, que poderá ser usada para acessar as guias adicionais. Para obter informações sobre os requisitos do ícone, consulte [O tamanho do ícone da barra de](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) guia suspreocupo developer.apple.com.

> [!TIP]
> O `TabbedRenderer` iOS tem um `GetIcon` método superridicularizado que pode ser usado para carregar ícones de guia de uma fonte especificada. Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage`. Além disso, versões selecionadas e não selecionadas de um ícone podem ser fornecidas.

No Android, a lista de guias aparece na parte superior da tela, e a área de detalhes está abaixo. Cada guia consiste em um título e um ícone, que deve ser um arquivo PNG com um canal alfa. No entanto, as guias podem ser movidas para a parte inferior da tela com uma plataforma específica. Se houver mais de cinco guias e a lista de guias estiver na parte inferior da tela, aparecerá uma guia *Mais* que poderá ser usada para acessar as guias adicionais. Para obter informações sobre os requisitos de [ícones,](https://material.io/components/tabs/#) consulte Guias em material.io e [suporte a diferentes densidades de pixels](https://developer.android.com/training/multiscreen/screendensities) em developer.android.com. Para obter informações sobre como mover as guias para a parte inferior da tela, consulte [Configuração De configuração Colocação e cor da barra de ferramentas](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md)da página de metas .

> [!TIP]
> O `TabbedPageRenderer` para Android AppCompat tem `GetIconDrawable` um método superridicularizado que pode ser `Drawable`usado para carregar ícones de guia de um personalizado . Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage` e funciona com barras de guia superior e inferior. Como alternativa, o método `SetTabIcon` substituível pode ser usado para carregar ícones de guia de um `Drawable` personalizado para barras de guia superior.

Na Universal Windows Platform (UWP), a lista de guias aparece na parte superior da tela, e a área de detalhes está abaixo. Cada guia consiste em um título. No entanto, os ícones podem ser adicionados a cada guia com uma plataforma específica. Para obter mais informações, consulte [Ícones de página guia no Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Criar uma TabbedPage

Duas abordagens podem ser [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)usadas para criar um:

- Preencha [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) o com uma [`Page`](xref:Xamarin.Forms.Page) coleção de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) infantis, como uma coleção de objetos. Para obter mais informações, consulte ['Preencher uma página'.](#populate-a-tabbedpage-with-a-page-collection)
- Atribuir uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar páginas para objetos na coleção. Para obter mais informações, consulte ['Preencher uma página de guia' com um modelo](#populate-a-tabbedpage-with-a-template).

Com ambas as [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) abordagens, a página exibirá cada página à medida que o usuário seleciona cada guia.

> [!IMPORTANT]
> Recomenda-se que um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deve ser [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) preenchido [`ContentPage`](xref:Xamarin.Forms.ContentPage) apenas com e instâncias. Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

Além disso, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) define as seguintes propriedades:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), do [`Color`](xref:Xamarin.Forms.Color)tipo, a cor de fundo da barra de guia.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), do [`Color`](xref:Xamarin.Forms.Color)tipo , a cor do texto na barra de guia.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), do [`Color`](xref:Xamarin.Forms.Color)tipo, a cor da guia quando ela é selecionada.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), do [`Color`](xref:Xamarin.Forms.Color)tipo, a cor da guia quando não é selecionada.

Todas essas propriedades são [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) apoiadas por objetos, o que significa que elas podem ser estilizadas, e as propriedades podem ser alvos de vinculações de dados.

> [!WARNING]
> Em [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)um [`Page`](xref:Xamarin.Forms.Page) , cada objeto `TabbedPage` é criado quando o é construído. Isso pode levar a uma má `TabbedPage` experiência do usuário, especialmente se a página raiz do aplicativo. No entanto, o Xamarin.Forms Shell permite que páginas acessadas através de uma barra de guias sejam criadas sob demanda, em resposta à navegação. Para obter mais informações, consulte [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Preencha uma página de guia com uma coleção de páginas

Um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser preenchido com [`Page`](xref:Xamarin.Forms.Page) uma coleção de objetos infantis, como uma coleção de [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos. Isso é conseguido `Page` adicionando [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) os objetos ao acervo. Isso é realizado em XAML da seguinte forma:

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
> A [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) propriedade [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) da classe, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) da qual `ContentProperty` deriva, é a de `MultiPage<T>`. Portanto, em XAML não é necessário atribuir explicitamente [`Page`](xref:Xamarin.Forms.Page) os `Children` objetos à propriedade.

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

Neste exemplo, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) o é preenchido [`Page`](xref:Xamarin.Forms.ContentPage) com dois objetos. A primeira criança [`ContentPage`](xref:Xamarin.Forms.ContentPage) é um objeto, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e a `ContentPage` segunda criança é um objeto que contém.

As capturas de [`ContentPage`](xref:Xamarin.Forms.ContentPage) tela [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)a seguir mostram um objeto em um:

[![Captura de tela de uma TabbedPage contendo três guias, no iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage com três guias")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage com três guias")

A seleção [`ContentPage`](xref:Xamarin.Forms.ContentPage) de outra guia exibe o objeto que representa a guia:

[![Captura de tela de uma tabbedPage contendo guias, no iOS e Android](tabbed-page-images/tabbedpage-week.png "Página de guias com guias")](tabbed-page-images/tabbedpage-week-large.png#lightbox "Página de guias com guias")

Na guia **Agendar,** o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é enrolado em um objeto.

> [!WARNING]
> Embora [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) um pode ser [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)colocado em um , não `TabbedPage` é `NavigationPage`recomendável colocar um em um . Isso ocorre porque, no iOS, um `UITabBarController` sempre funciona como um wrapper para o `UINavigationController`. Para obter mais informações, confira [Interfaces combinadas do controlador de exibição](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) na Biblioteca do Desenvolvedor do iOS.

## <a name="navigate-within-a-tab"></a>Navegue dentro de uma guia

A navegação pode ser realizada dentro [`ContentPage`](xref:Xamarin.Forms.ContentPage) de uma guia, desde que o objeto esteja enrolado em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) objeto. Isso é feito invocando [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) o [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) método [`ContentPage`](xref:Xamarin.Forms.ContentPage) sobre a propriedade do objeto:

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

A página a ser navegada é [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) especificada como o argumento para o método. Neste exemplo, `UpcomingAppointmentsPage` a página é empurrada para a pilha de navegação, onde se torna a página ativa:

[![Captura de tela de navegação dentro de uma guia, no iOS e Android](tabbed-page-images/tabbedpage-upcoming.png "Navegação com guias Page em uma guia")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navegação com guias Page em uma guia")

Para obter mais informações sobre [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) a realização da navegação usando a classe, consulte [Navegação Hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Preencha uma página de guia com um modelo

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser preenchido com páginas atribuindo uma [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) coleção de dados [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade e atribuindo [`Page`](xref:Xamarin.Forms.Page) um à propriedade que modela os dados como objetos. Isso é realizado em XAML da seguinte forma:

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

Neste exemplo, cada guia consiste [`ContentPage`](xref:Xamarin.Forms.ContentPage) em [`Image`](xref:Xamarin.Forms.Image) um [`Label`](xref:Xamarin.Forms.Label) objeto que usa e objetos para exibir dados para a guia:

[![Captura de tela de uma página modelada, no iOS e Android](tabbed-page-images/tabbedpage-template.png "Página de guia sinuosa")](tabbed-page-images/tabbedpage-template-large.png#lightbox "Página de guia sinuosa")

A seleção [`ContentPage`](xref:Xamarin.Forms.ContentPage) de outra guia exibe o objeto que representa a guia.

## <a name="related-links"></a>Links relacionados

- [TabbedPageWithNavigationPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Variedades de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API de página guiada](xref:Xamarin.Forms.TabbedPage)
