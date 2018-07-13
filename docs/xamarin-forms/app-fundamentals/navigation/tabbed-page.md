---
title: Página com guias do xamarin. Forms
description: O xamarin. Forms TabbedPage consiste em uma lista de guias e uma área maior de detalhes, com cada guia Carregando conteúdo na área de detalhes. Este artigo demonstra como usar uma TabbedPage para navegar por meio de uma coleção de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3eb978780222da2050fc91dfa41c68ef4bd3b6f4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996289"
---
# <a name="xamarinforms-tabbed-page"></a>Página com guias do xamarin. Forms

_O xamarin. Forms TabbedPage consiste em uma lista de guias e uma área maior de detalhes, com cada guia Carregando conteúdo na área de detalhes. Este artigo demonstra como usar uma TabbedPage para navegar por meio de uma coleção de páginas._

## <a name="overview"></a>Visão geral

As capturas de tela a seguir mostram uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) em cada plataforma:

![](tabbed-page-images/tab1.png "Exemplo de TabbedPage")

As capturas de tela a seguir se concentram no formato de guia em cada plataforma:

![](tabbed-page-images/tabbedpage-components.png "TabbedPage guia componentes")

O layout de um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)e suas guias, depende da plataforma:

- No iOS, é exibida na lista de guias na parte inferior da tela e a área de detalhes está acima. Cada guia também tem uma imagem de ícone que deve ser um 30 x 30 PNG com transparência para resolução normal, 60 x 60 para alta resolução e 90 x 90 para iPhone 6 Plus resolução. Se houver mais de cinco guias, uma *mais* guia será exibida, que pode ser usado para acessar as guias adicionais. Para obter mais informações sobre o carregamento de imagens em um aplicativo xamarin. Forms, consulte [trabalhando com imagens](~/xamarin-forms/user-interface/images.md). Para obter mais informações sobre os requisitos de ícone, consulte [criando aplicativos com guias](~/ios/user-interface/controls/creating-tabbed-applications.md).

    > [!NOTE]
  > Observe que o `TabbedRenderer` para iOS tem um substituível `GetIcon` método que pode ser usado para carregar ícones de guia de uma origem especificada. Essa substituição torna possível usar as imagens de SVG como ícones em uma `TabbedPage`. Além disso, selecionadas e versões de um ícone podem ser fornecidas.

- No Android, a lista de guias aparece na parte superior da tela, por padrão, e a área de detalhes está abaixo. No entanto, a lista de guias pode ser movida para a parte inferior da tela com uma plataforma específica. Para obter mais informações, consulte [configuração TabbedPage barra de ferramentas de posicionamento e a cor](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar).

    > [!NOTE]
  > Observe que, ao usar AppCompat no Android, cada guia também exibirá um ícone. Além disso, o `TabbedPageRenderer` para AppCompat Android tem uma substituível `SetTabIcon` método que pode ser usado para carregar ícones de guia de um personalizado `Drawable`. Essa substituição torna possível usar as imagens de SVG como ícones em uma `TabbedPage`.

- No Windows tablet fatores de forma, as guias não são sempre visíveis e os usuários precisam passar o dedo para baixo (ou com o botão direito do mouse, se eles tiverem um mouse anexado) para exibir as guias em um `TabbedPage` (conforme mostrado abaixo).

![](tabbed-page-images/windows-tabs.png "Guias de TabbedPage no Windows")

## <a name="creating-a-tabbedpage"></a>Criando uma TabbedPage

Duas abordagens podem ser usadas para criar uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

- [Popular](#Populating_a_TabbedPage_with_a_Page_Collection) as [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) com uma coleção de filhos [ `Page` ](xref:Xamarin.Forms.Page) objetos, como uma coleção de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instâncias.
- [Atribua](#Populating_a_TabbedPage_with_a_Template) uma coleção para o [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade e atribuir uma [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para o [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade a retornar páginas para objetos na coleção.

Com as duas abordagens, o [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) exibirá cada página como o usuário seleciona cada guia.

> [!NOTE]
> É recomendável que um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) deve ser preenchido com [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) e [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)somente instâncias. Isso ajudará a garantir uma experiência de usuário consistente em todas as plataformas.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Preenchendo uma TabbedPage com uma coleção de página

Mostra o exemplo de código XAML abaixo uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) construídas populá-lo com uma coleção de filhos [ `Page` ](xref:Xamarin.Forms.Page) objetos:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" Icon="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

O exemplo de código a seguir mostra o equivalente [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) criado em c#:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.Icon = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

O [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) é preenchida com duas filho [ `Page` ](xref:Xamarin.Forms.Page) objetos. O primeiro filho é um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância e a segunda guia é uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que contém um `ContentPage` instância.

> [!NOTE]
> O [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) não oferece suporte a virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `TabbedPage` contém muitos elementos filho.

As capturas de tela a seguir mostram os `TodayPage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância, que é exibida no *hoje* guia:

![](tabbed-page-images/today-page.png "ContentPage em uma TabbedPage")

Selecionando o *agenda* guia exibe os `SchedulePage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância, que é encapsulada em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) da instância e é mostrada no captura de tela a seguir:

![](tabbed-page-images/schedule-page.png "NavigationPage em uma TabbedPage")

Para obter informações sobre o layout de um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), consulte [navegação executando](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Embora seja aceitável para colocar uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) em uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), não é recomendável para colocar um `TabbedPage` em um `NavigationPage`. Isso ocorre porque, no iOS, um `UITabBarController` sempre atua como um wrapper para o `UINavigationController`. Para obter mais informações, consulte [Interfaces de controlador de exibição combinados](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) na biblioteca do desenvolvedor do iOS.

#### <a name="navigation-inside-a-tab"></a>Navegação dentro de uma guia

Navegação pode ser executada a partir da segunda guia invocando o [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) método no [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade do [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Isso faz com que a instância `UpcomingAppointmentsPage` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas capturas de tela seguir:

![](tabbed-page-images/navigationpage.png "Navegação dentro de uma guia")

Para obter mais informações sobre como executar a navegação usando o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) da classe, consulte [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Preenchendo uma TabbedPage com um modelo

Mostra o exemplo de código XAML abaixo uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) construído atribuindo um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para o [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade a retornar páginas para objetos na coleção:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" Icon="monkeyicon.png">
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

O [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) é preenchida com dados, definindo o [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade no construtor para o arquivo code-behind:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

O exemplo de código a seguir mostra o equivalente [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) criado em c#:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        Icon = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
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

Cada guia exibe uma [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) que usa uma série de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) e [ `Label` ](xref:Xamarin.Forms.Label) instâncias para exibir dados para a guia. As capturas de tela a seguir mostram o conteúdo para o *Tamarin* guia:

![](tabbed-page-images/tab3.png "Preenchendo uma TabbedPage com um modelo")

Selecionar outra guia, em seguida, exibe o conteúdo para essa guia.

> [!NOTE]
> O [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) não oferece suporte a virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `TabbedPage` contém muitos elementos filho.

Para obter mais informações sobre o [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), consulte [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) de xamarin. Forms livro Charles Petzold.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar uma TabbedPage para navegar por meio de uma coleção de páginas. O xamarin. Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) consiste em uma lista de guias e uma área maior de detalhe, com cada guia Carregando conteúdo na área de detalhes.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
