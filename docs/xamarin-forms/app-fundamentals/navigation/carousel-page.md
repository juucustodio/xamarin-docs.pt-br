---
title: Página de carrossel do Xamarin.Forms
description: A CarouselPage do Xamarin.Forms é uma página em que os usuários podem passar o dedo de um lado ao outro para navegar por páginas de conteúdo, como uma galeria. Este artigo demonstra como usar uma CarouselPage para navegar por uma coleção de páginas.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 5cfaeb8589514972dfcccd11009a02c00f95bfac
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72696438"
---
# <a name="xamarinforms-carousel-page"></a>Página de carrossel do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_O Xamarin.Forms CarrosselPage é uma página que os usuários podem deslizar de um lado para o outro para navegar por páginas de conteúdo, como uma galeria. Este artigo demonstra como usar um CarrosselPage para navegar através de uma coleção de páginas._

> [!IMPORTANT]
> O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) foi substituído pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView), que fornece um layout rolável onde os usuários podem deslizar para mover através de uma coleção de itens. Para obter mais `CarouselView`informações sobre o , consulte [Xamarin.Forms CarrosselView](~/xamarin-forms/user-interface/carouselview/index.md).

As capturas de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) tela a seguir mostram um em cada plataforma:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

O layout [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) de a é idêntico em cada plataforma. As páginas podem ser navegadas passando o dedo da direita para a esquerda para navegar para frente na coleção e passando o dedo da esquerda para a direita para navegar para trás. As capturas de tela a [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) seguir mostram a primeira página em uma instância:

![](carousel-page-images/firstpage.png "CarouselPage First Item")

Passar o dedo da direita para a esquerda leva para a segunda página, conforme mostrado nas capturas de tela seguir:

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

Passar o dedo da direita para a esquerda novamente leva para a terceira página, enquanto passar o dedo da esquerda para a direita retorna à página anterior.

> [!NOTE]
> O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não suporta virtualização de IU. Portanto, o desempenho poderá ser afetado se a `CarouselPage` contiver muitos elementos filho.

Se [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) um estiver [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) incorporado na [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)página [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) de a, `false` a propriedade deve `CarouselPage` ser `MasterDetailPage`definida para evitar conflitos de gestos entre o e o .

Para obter mais [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)informações sobre o , consulte [O Capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do livro Xamarin.Forms de Charles Petzold.

## <a name="create-a-carouselpage"></a>Criar uma CarouselPage

Duas abordagens podem ser [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)usadas para criar um:

- [Popular](#Populating_a_CarouselPage_with_a_Page_Collection) uma `CarouselPage` com uma coleção de instancias filhas de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Atribuir](#Populating_a_CarouselPage_with_a_Template) uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar instâncias de [`ContentPage`](xref:Xamarin.Forms.ContentPage) para objetos na coleção.

Com as duas abordagens, a `CarouselPage` exibirá uma página por vez, com uma interação de passar o dedo levando para a próxima página a ser exibida.

> [!NOTE]
> A [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) só pode ser [`ContentPage`](xref:Xamarin.Forms.ContentPage) preenchido `ContentPage` com instâncias, ou derivativos.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populate-a-carouselpage-with-a-page-collection"></a>Popular uma CarouselPage com uma coleção de páginas

O exemplo de código XAML a seguir mostra um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) que exibe três [`ContentPage`](xref:Xamarin.Forms.ContentPage) instâncias:

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

Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) um [`Label`](xref:Xamarin.Forms.Label) simplesmente exibe um [`BoxView`](xref:Xamarin.Forms.BoxView) para uma cor específica e uma dessa cor.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populate-a-carouselpage-with-a-template"></a>Popular uma CarouselPage com um modelo

O exemplo de código XAML a seguir mostra um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) construído atribuindo um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade para devolver páginas para objetos na coleção:

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

O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é preenchido com dados [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) definindo a propriedade no construtor para o arquivo de código atrás:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

O exemplo de código [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) a seguir mostra o equivalente criado em C#:

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

Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) um [`Label`](xref:Xamarin.Forms.Label) simplesmente exibe um [`BoxView`](xref:Xamarin.Forms.BoxView) para uma cor específica e uma dessa cor.

## <a name="related-links"></a>Links relacionados

- [Variedades de páginas](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
