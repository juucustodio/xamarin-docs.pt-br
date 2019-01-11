---
title: Página com guias do Xamarin.Forms
description: A TabbedPage do Xamarin.Forms consiste em uma lista de guias e uma área do detalhe maior, com cada guia carregando conteúdo na área do detalhe. Este artigo demonstra como usar uma TabbedPage para navegar por uma coleção de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 4e2d33276c865695d70abb2d8e00b3b80d446839
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207980"
---
# <a name="xamarinforms-tabbed-page"></a>Página com guias do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)

_A TabbedPage do Xamarin.Forms consiste em uma lista de guias e uma área do detalhe maior, com cada guia carregando conteúdo na área do detalhe. Este artigo demonstra como usar uma TabbedPage para navegar por uma coleção de páginas._

## <a name="overview"></a>Visão geral

As seguintes capturas de tela mostram uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) em cada plataforma:

![](tabbed-page-images/tab1.png "Exemplo de TabbedPage")

As seguintes capturas de tela se concentram no formato da guia em cada plataforma:

![](tabbed-page-images/tabbedpage-components.png "Componentes da guia de TabbedPage")

O layout de uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), e suas guias, depende da plataforma:

- No iOS, a lista de guias é exibida na parte inferior da tela e a área do detalhe fica acima dela. Cada guia também tem uma imagem de ícone que deve ser um PNG 30x30 com transparência para resolução normal, 60x60 para alta resolução e 90x90 para resolução no iPhone 6 Plus. Se houver mais de cinco guias, uma guia *Mais* será exibida, que poderá ser usada para acessar as guias adicionais. Para obter mais informações sobre como carregar imagens em um aplicativo Xamarin.Forms, confira [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md). Para obter mais informações sobre os requisitos de ícone, confira [Criando aplicativos com guias](~/ios/user-interface/controls/creating-tabbed-applications.md).

  > [!NOTE]
  > Observe que o `TabbedRenderer` para iOS tem um método `GetIcon` substituível que pode ser usado para carregar ícones de guia de uma origem especificada. Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage`. Além disso, versões selecionadas e não selecionadas de um ícone podem ser fornecidas.

- No Android, a lista de guias é exibida na parte superior da tela por padrão e a área do detalhe fica abaixo dela. No entanto, a lista de guias pode ser movida para a parte inferior da tela com um código específico da plataforma. Para obter mais informações, confira [Definindo o posicionamento e a cor da barra de ferramentas da TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

  > [!NOTE]
  > Observe que, ao usar AppCompat no Android, cada guia também exibirá um ícone. Além disso, o `TabbedPageRenderer` para Android AppCompat tem um método `GetIconDrawable` substituível que pode ser usado para carregar ícones de guia de um `Drawable` personalizado. Essa substituição possibilita o uso de imagens SVG como ícones em uma `TabbedPage` e funciona com barras de guia superior e inferior. Como alternativa, o método `SetTabIcon` substituível pode ser usado para carregar ícones de guia de um `Drawable` personalizado para barras de guia superior.

- Em fatores forma de tablets Windows, as guias não ficam sempre visíveis e os usuários precisam passar o dedo para baixo (ou clicar com o botão direito do mouse, caso tenham um mouse anexado) para exibir as guias em uma `TabbedPage` (conforme mostrado abaixo).

![](tabbed-page-images/windows-tabs.png "Guias de TabbedPage no Windows")

## <a name="creating-a-tabbedpage"></a>Criando uma TabbedPage

Duas abordagens podem ser usadas para criar uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

- [Popular](#Populating_a_TabbedPage_with_a_Page_Collection) a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) com uma coleção de objetos [`Page`](xref:Xamarin.Forms.Page) filhos, como uma coleção de [`ContentPage`](xref:Xamarin.Forms.ContentPage) instâncias.
- [Atribuir](#Populating_a_TabbedPage_with_a_Template) uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar páginas para objetos na coleção.

Com as duas abordagens, a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) exibirá cada página conforme o usuário selecionar cada guia.

> [!NOTE]
> É recomendável popular uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) somente com as instâncias [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Isso ajudará a garantir uma experiência do usuário consistente em todas as plataformas.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Populando uma TabbedPage com uma coleção de páginas

O seguinte exemplo de código XAML mostra uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) construída populando-a com uma coleção de objetos [`Page`](xref:Xamarin.Forms.Page) filho:

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

O seguinte exemplo de código mostra a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) equivalente criada em C#:

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

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é populada com dois objetos [`Page`](xref:Xamarin.Forms.Page) filho. O primeiro filho é uma instância [`ContentPage`](xref:Xamarin.Forms.ContentPage) e a segunda guia é uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contém uma instância `ContentPage`.

> [!NOTE]
> A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) não dá suporte à virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `TabbedPage` contiver muitos elementos filho.

As seguintes capturas de tela mostram a instância `TodayPage` [`ContentPage`](xref:Xamarin.Forms.ContentPage), que é mostrada na guia *Hoje*:

![](tabbed-page-images/today-page.png "ContentPage em uma TabbedPage")

A seleção da guia *Agendamento* exibe a instância `SchedulePage` [`ContentPage`](xref:Xamarin.Forms.ContentPage), que é encapsulada em uma instância [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), e é mostrada na seguinte captura de tela:

![](tabbed-page-images/schedule-page.png "NavigationPage em uma TabbedPage")

Para obter informações sobre o layout de uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), confira [Executando a navegação](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Embora seja aceitável colocar uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), não é recomendável colocar uma `TabbedPage` em uma `NavigationPage`. Isso ocorre porque, no iOS, um `UITabBarController` sempre funciona como um wrapper para o `UINavigationController`. Para obter mais informações, confira [Interfaces combinadas do controlador de exibição](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) na Biblioteca do Desenvolvedor do iOS.

#### <a name="navigation-inside-a-tab"></a>Navegação dentro de uma guia

A navegação pode ser executada na segunda guia invocando o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) na propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) da instância [`ContentPage`](xref:Xamarin.Forms.ContentPage), conforme demonstrado no seguinte exemplo de código:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Isso faz com que a instância `UpcomingAppointmentsPage` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas seguintes capturas de tela:

![](tabbed-page-images/navigationpage.png "Navegação dentro de uma guia")

Para obter mais informações sobre como executar a navegação usando a classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), confira [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Populando uma TabbedPage com um modelo

O exemplo de código XAML a seguir mostra uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) construída pela atribuição de um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar páginas para objetos na coleção:

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

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é populada com os dados, definindo a propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) no construtor para o arquivo code-behind:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

O seguinte exemplo de código mostra a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) equivalente criada em C#:

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

Cada guia exibe uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) que usa uma série de instâncias [`StackLayout`](xref:Xamarin.Forms.StackLayout) e [`Label`](xref:Xamarin.Forms.Label) para exibir os dados para a guia. As seguintes capturas de tela mostram o conteúdo da guia *Tamarin*:

![](tabbed-page-images/tab3.png "Populando uma TabbedPage com um modelo")

A seleção de outra guia exibe o conteúdo dessa guia.

> [!NOTE]
> A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) não dá suporte à virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `TabbedPage` contiver muitos elementos filho.

Para obter mais informações sobre a [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), confira o [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do livro sobre o Xamarin.Forms de Charles Petzold.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar uma TabbedPage para navegar por uma coleção de páginas. A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) do Xamarin.Forms consiste em uma lista de guias e uma área do detalhe maior, com cada guia carregando conteúdo na área do detalhe.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
