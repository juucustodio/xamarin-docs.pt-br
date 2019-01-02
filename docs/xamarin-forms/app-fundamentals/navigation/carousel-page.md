---
title: Página de carrossel do Xamarin.Forms
description: A CarouselPage do Xamarin.Forms é uma página em que os usuários podem passar o dedo de um lado ao outro para navegar por páginas de conteúdo, como uma galeria. Este artigo demonstra como usar uma CarouselPage para navegar por uma coleção de páginas.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: b0dfcd2cd50cc70ea7735988236506e18ccf7937
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051300"
---
# <a name="xamarinforms-carousel-page"></a>Página de carrossel do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)

_A CarouselPage do Xamarin.Forms é uma página em que os usuários podem passar o dedo de um lado ao outro para navegar por páginas de conteúdo, como uma galeria. Este artigo demonstra como usar uma CarouselPage para navegar por uma coleção de páginas._

## <a name="overview"></a>Visão geral

As seguintes capturas de tela mostram uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) em cada plataforma:

![](carousel-page-images/thirdpage.png "Terceiro item de CarouselPage")

O layout de uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é idêntico em todas as plataformas. As páginas podem ser navegadas passando o dedo da direita para a esquerda para navegar para frente na coleção e passando o dedo da esquerda para a direita para navegar para trás. As capturas de tela a seguir mostram a primeira página em uma instância de uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage):

![](carousel-page-images/firstpage.png "Primeiro item de CarouselPage")

Passar o dedo da direita para a esquerda leva para a segunda página, conforme mostrado nas capturas de tela seguir:

![](carousel-page-images/secondpage.png "Segundo item de CarouselPage")

Passar o dedo da direita para a esquerda novamente leva para a terceira página, enquanto passar o dedo da esquerda para a direita retorna à página anterior.

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Criando uma CarouselPage

Duas abordagens podem ser usadas para criar uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage):

- [Popular](#Populating_a_CarouselPage_with_a_Page_Collection) uma `CarouselPage` com uma coleção de instancias filhas de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Atribuir](#Populating_a_CarouselPage_with_a_Template) uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar instâncias de [`ContentPage`](xref:Xamarin.Forms.ContentPage) para objetos na coleção.

Com as duas abordagens, a `CarouselPage` exibirá uma página por vez, com uma interação de passar o dedo levando para a próxima página a ser exibida.

> [!NOTE]
> Uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) pode ser populada apenas com instâncias de [`ContentPage`](xref:Xamarin.Forms.ContentPage) ou derivados de `ContentPage`.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Populando uma CarouselPage com uma coleção de páginas

O exemplo de código XAML a seguir mostra um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) que exibe três instâncias de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

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

O exemplo de código a seguir mostra a interface do usuário equivalente em C#:

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

Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) simplesmente exibe um [`Label`](xref:Xamarin.Forms.Label) para uma cor específica e um [`BoxView`](xref:Xamarin.Forms.BoxView) dessa cor.

> [!NOTE]
> A [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não dá suporte à virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `CarouselPage` contiver muitos elementos filho.

Se uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) for inserida na página [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), a propriedade [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) deverá ser definida como `false` para evitar conflitos de gesto entre o `CarouselPage` e o `MasterDetailPage`.

Para obter mais informações sobre a [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), confira o [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do livro sobre o Xamarin.Forms de Charles Petzold.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Populando uma CarouselPage com um modelo

O exemplo de código XAML a seguir mostra uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) construída pela atribuição de um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar páginas para objetos na coleção:

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

A [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é populada com os dados, definindo a propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) no construtor para o arquivo code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

O seguinte exemplo de código mostra a [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) equivalente criada em C#:

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

Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) simplesmente exibe um [`Label`](xref:Xamarin.Forms.Label) para uma cor específica e um [`BoxView`](xref:Xamarin.Forms.BoxView) dessa cor.

> [!NOTE]
> A [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não dá suporte à virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `CarouselPage` contiver muitos elementos filho.

Se uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) for inserida na página [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), a propriedade [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) deverá ser definida como `false` para evitar conflitos de gesto entre o `CarouselPage` e o `MasterDetailPage`.

Para obter mais informações sobre a [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), confira o [Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do livro sobre o Xamarin.Forms de Charles Petzold.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) para navegar por uma coleção de páginas. A `CarouselPage` é uma página em que os usuários podem passar o dedo de um lado ao outro para navegar por páginas de conteúdo, assim como uma galeria.


## <a name="related-links"></a>Links relacionados

- [Variedades de página](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
