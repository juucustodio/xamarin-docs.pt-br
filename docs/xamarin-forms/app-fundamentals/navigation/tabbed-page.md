---
title: Página com guias
description: O TabbedPage xamarin. Forms consiste em uma lista de guias e uma área de detalhes maior, com cada guia Carregando conteúdo na área de detalhes. Este artigo demonstra como usar um TabbedPage para navegar por meio de uma coleção de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 11287d38ec0e01e068ca385c92e6a6efdc323aeb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="tabbed-page"></a>Página com guias

_O TabbedPage xamarin. Forms consiste em uma lista de guias e uma área de detalhes maior, com cada guia Carregando conteúdo na área de detalhes. Este artigo demonstra como usar um TabbedPage para navegar por meio de uma coleção de páginas._

## <a name="overview"></a>Visão geral

O capturas de tela a seguir mostram um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) em cada plataforma:

![](tabbed-page-images/tab1.png "Exemplo de TabbedPage")

As capturas de tela a seguir se concentrar no formato de guia em cada plataforma:

![](tabbed-page-images/tabbedpage-components.png "TabbedPage guia componentes")

O layout de um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)e suas guias, depende da plataforma:

- No iOS, é exibida a lista de guias na parte inferior da tela e a área de detalhes está acima. Cada guia também tem uma imagem de ícone que deve ser um 30 x 30 PNG com transparência para resolução normal, 60 x 60 para alta resolução e 90, 90 para iPhone 6 Plus resolução. Se houver mais de cinco guias, uma *mais* guia aparecerá, que pode ser usado para acessar as guias adicionais. Para obter mais informações sobre como carregar imagens em um aplicativo xamarin. Forms, consulte [trabalhando com imagens](~/xamarin-forms/user-interface/images.md). Para obter mais informações sobre os requisitos de ícone, consulte [criando aplicativos com guias](~/ios/user-interface/controls/creating-tabbed-applications.md).

    > [!NOTE]
  > Observe que o `TabbedRenderer` para iOS tem um substituível `GetIcon` método que pode ser usado para carregar ícones de guia de uma origem especificada. Essa substituição é torna possível usar as imagens de SVG como ícones em uma `TabbedPage`. Além disso, as versões selecionadas e não selecionadas de um ícone podem ser fornecidas.

- No Android, a lista de guias é exibida na parte superior da tela e a área de detalhes está abaixo. Os nomes das guias são maiuscula automaticamente, e o usuário pode rolar a coleção de guias se houver muito grande para caber na tela.

    > [!NOTE]
  > Observe que, ao usar AppCompat no Android, cada guia também exibirá um ícone. Além disso, o `TabbedPageRenderer` de AppCompat Android tem um substituível `SetTabIcon` método que pode ser usado para carregar os ícones de guia de um personalizado `Drawable`. Essa substituição é torna possível usar as imagens de SVG como ícones em uma `TabbedPage`.

- No Windows Phone, a lista de guias é exibida na parte superior da tela e a área de detalhes está abaixo. Na guia nomes são automaticamente convertidos em minúsculas e o usuário pode rolar a coleção de guias se houver muito grande para caber na tela.
- No Windows tablet-fatores de forma, as guias não estão sempre visíveis e os usuários precisam passe o dedo para baixo (ou com o botão direito do mouse, se tiverem um mouse anexado) para exibir as guias em um `TabbedPage` (conforme mostrado abaixo).

![](tabbed-page-images/windows-tabs.png "Guias de TabbedPage no Windows")

## <a name="creating-a-tabbedpage"></a>Criando um TabbedPage

Duas abordagens podem ser usadas para criar um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/):

- [Popular](#Populating_a_TabbedPage_with_a_Page_Collection) o [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) com uma coleção de filhos [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objetos, como uma coleção de [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instâncias.
- [Atribuir](#Populating_a_TabbedPage_with_a_Template) uma coleção para o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propriedade e atribuir um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) para o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propriedade a retornar páginas para objetos na coleção.

Com ambas as abordagens de [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) exibirá cada página como o usuário seleciona cada guia.

> [!NOTE]
> Recomenda-se que um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) devem ser preenchidos com [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) e [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)somente instâncias. Isso ajudará a garantir uma experiência de usuário consistente em todas as plataformas.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Preenchendo um TabbedPage com uma coleção de página

O exemplo do código XAML seguinte mostra um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) construído por populá-lo com uma coleção de filhos [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objetos:

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

O exemplo de código a seguir mostra o equivalente [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) criado em c#:

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

O [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) é populada com dois filhos [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objetos. O primeiro filho é um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância e a segunda guia é um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) que contém um `ContentPage` instância.

> [!NOTE]
> O [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) não oferece suporte a virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se o `TabbedPage` contém muitos elementos filho.

O capturas de tela a seguir mostram o `TodayPage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância, que é exibida no *hoje* guia:

![](tabbed-page-images/today-page.png "ContentPage em um TabbedPage")

Selecionando o *agenda* guia exibe o `SchedulePage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância, que é encapsulada em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância e é mostrada no captura de tela abaixo:

![](tabbed-page-images/schedule-page.png "NavigationPage em um TabbedPage")

Para obter informações sobre o layout de um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), consulte [navegação executando](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Enquanto ele é aceitável para colocar um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) em uma [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), não é recomendado para colocar um `TabbedPage` em um `NavigationPage`. Isso ocorre porque, no iOS, um `UITabBarController` sempre atua como um wrapper para o `UINavigationController`. Para obter mais informações, consulte [combinados Interfaces do modo de exibição do controlador](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) na biblioteca do desenvolvedor iOS.

#### <a name="navigation-inside-a-tab"></a>Navegação dentro de uma guia

Navegação pode ser executada a partir da segunda guia invocando o [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) método no [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Isso faz com que a instância `UpcomingAppointmentsPage` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas capturas de tela seguir:

![](tabbed-page-images/navigationpage.png "Navegação dentro de uma guia")

Para obter mais informações sobre como executar a navegação usando o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) de classe, consulte [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Preenchendo um TabbedPage com um modelo

Mostra o exemplo seguinte do código XAML um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) construído, atribuindo um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) para o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propriedade a retornar páginas para objetos na coleção:

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

O [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) é preenchido com dados definindo o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propriedade no construtor para o arquivo code-behind:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

O exemplo de código a seguir mostra o equivalente [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) criado em c#:

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

Cada guia exibe uma [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que usa uma série de [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias para exibir dados da guia. As capturas de tela a seguir mostram o conteúdo para o *Tamarin* guia:

![](tabbed-page-images/tab3.png "Preenchendo um TabbedPage com um modelo")

Selecionar outra guia, em seguida, exibe o conteúdo para a guia.

> [!NOTE]
> O [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) não oferece suporte a virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se o `TabbedPage` contém muitos elementos filho.

Para obter mais informações sobre o [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), consulte [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do catálogo do Charles Petzold xamarin. Forms.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um TabbedPage para navegar por meio de uma coleção de páginas. O xamarin. Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) consiste em uma lista de guias e uma área de detalhes maior, com cada guia Carregando conteúdo na área de detalhes.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)
