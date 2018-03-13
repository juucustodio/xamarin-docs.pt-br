---
title: "Página de carrossel"
description: "O CarouselPage xamarin. Forms é uma página que os usuários podem passar de lado a lado para navegar pelas páginas de conteúdo, como uma galeria. Este artigo demonstra como usar um CarouselPage para navegar por meio de uma coleção de páginas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c4f8d70625fd8ee0e07832f8bdb3b7e502b5b38f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="carousel-page"></a>Página de carrossel

_O CarouselPage xamarin. Forms é uma página que os usuários podem passar de lado a lado para navegar pelas páginas de conteúdo, como uma galeria. Este artigo demonstra como usar um CarouselPage para navegar por meio de uma coleção de páginas._

## <a name="overview"></a>Visão geral

O capturas de tela a seguir mostram um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) em cada plataforma:

![](carousel-page-images/thirdpage.png "CarouselPage Thid Item")

O layout de um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) é idêntico em cada plataforma. Páginas podem ser navegadas por meio de passando o dedo da direita para a esquerda para navegar encaminhamentos por meio da coleção e passando esquerda para a direita para navegar para trás por meio da coleção. As capturas de tela a seguir mostram a primeira página um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) instância:

![](carousel-page-images/firstpage.png "CarouselPage primeiro Item")

Passar o dedo da direita para a esquerda move para a segunda página, conforme mostrado nas capturas de tela seguir:

![](carousel-page-images/secondpage.png "CarouselPage segundo Item")

Passar o dedo da direita para esquerda novamente move para a terceira página, ao passar o dedo da esquerda para a direita retorna à página anterior.

<!--
> [!NOTE]
> The [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Criando um CarouselPage

Duas abordagens podem ser usadas para criar um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/):

- [Popular](#Populating_a_CarouselPage_with_a_Page_Collection) o `CarouselPage` com uma coleção de filhos [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instâncias.
- [Atribuir](#Populating_a_CarouselPage_with_a_Template) uma coleção para o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propriedade e atribuir um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) para o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propriedade para retornar [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instâncias de objetos na coleção.

Com as duas abordagens a `CarouselPage` será, em seguida, exibir cada página, por sua vez, com uma interação passe o dedo para mover para a próxima página a ser exibida. Essa experiência de navegação se sentirão natural e familiar para os usuários do Windows Phone.

> [!NOTE]
> Um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) só pode ser preenchido com [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instâncias, ou `ContentPage` derivados.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Preenchendo um CarouselPage com uma coleção de página

O exemplo do código XAML seguinte mostra um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) que exibe três [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instâncias:

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

O exemplo de código a seguir mostra a interface do usuário equivalente em c#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

Cada [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) simplesmente exibe um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para uma determinada cor e um [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) dessa cor.

> [!NOTE]
> O [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) não oferece suporte a virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se o `CarouselPage` contém muitos elementos filho.

Se um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) é incorporada a [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página de um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), o [ `MasterDetailPage.IsGestureEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty/) propriedade deve ser definida como `false` para evitar conflitos de gesto entre o `CarouselPage` e `MasterDetailPage`.

Para obter mais informações sobre o [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), consulte [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do catálogo do Charles Petzold xamarin. Forms.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Preenchendo um CarouselPage com um modelo

Mostra o exemplo seguinte do código XAML um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) construído, atribuindo um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) para o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propriedade a retornar páginas para objetos na coleção:

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

O [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) é preenchido com dados definindo o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propriedade no construtor para o arquivo code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

O exemplo de código a seguir mostra o equivalente [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) criado em c#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

Cada [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) simplesmente exibe um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para uma determinada cor e um [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) dessa cor.

> [!NOTE]
> O [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) não oferece suporte a virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se o `CarouselPage` contém muitos elementos filho.

Se um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) é incorporada a [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página de um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), o [ `MasterDetailPage.IsGestureEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty/) propriedade deve ser definida como `false` para evitar conflitos de gesto entre o `CarouselPage` e `MasterDetailPage`.

Para obter mais informações sobre o [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), consulte [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do catálogo do Charles Petzold xamarin. Forms.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar um [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) para navegar por meio de uma coleção de páginas. O `CarouselPage` é uma página que os usuários podem passar de lado a lado para navegar pelas páginas de conteúdo, como uma galeria e fornece uma experiência de navegação que se parece natural e familiar para os usuários do Windows Phone.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)
