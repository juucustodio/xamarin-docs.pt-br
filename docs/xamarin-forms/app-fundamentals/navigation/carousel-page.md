---
Título: " Xamarin.Forms página do carrossel" Descrição: "o Xamarin.Forms CarouselPage é uma página que os usuários podem passar do lado a lado para navegar pelas páginas de conteúdo, como uma galeria. Este artigo demonstra como usar um CarouselPage para navegar por uma coleção de páginas. "
MS. Prod: xamarin MS. AssetID: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-carousel-page"></a>Xamarin.FormsPágina do carrossel

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_O Xamarin.Forms CarouselPage é uma página que os usuários podem passar do lado a lado para navegar pelas páginas de conteúdo, como uma galeria. Este artigo demonstra como usar um CarouselPage para navegar por uma coleção de páginas._

> [!IMPORTANT]
> O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) foi substituído pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) , que fornece um layout rolável, onde os usuários podem passar a passar por uma coleção de itens. Para obter mais informações sobre o `CarouselView` , consulte [ Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md).

As capturas de tela a seguir mostram um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) em cada plataforma:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

O layout de um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é idêntico em cada plataforma. As páginas podem ser navegadas passando o dedo da direita para a esquerda para navegar para frente na coleção e passando o dedo da esquerda para a direita para navegar para trás. As capturas de tela a seguir mostram a primeira página em uma [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) instância:

![](carousel-page-images/firstpage.png "CarouselPage First Item")

Passar o dedo da direita para a esquerda leva para a segunda página, conforme mostrado nas capturas de tela seguir:

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

Passar o dedo da direita para a esquerda novamente leva para a terceira página, enquanto passar o dedo da esquerda para a direita retorna à página anterior.

> [!NOTE]
> O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) não oferece suporte à virtualização de interface do usuário. Portanto, o desempenho poderá ser afetado se a `CarouselPage` contiver muitos elementos filho.

Se um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) for inserido na [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) página de a [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , a [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) Propriedade deverá ser definida como `false` para evitar conflitos de gesto entre o `CarouselPage` e o `MasterDetailPage` .

Para obter mais informações sobre o [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) , consulte o [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) do livro de Charles Petzold Xamarin.Forms .

## <a name="create-a-carouselpage"></a>Criar uma CarouselPage

Duas abordagens podem ser usadas para criar um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) :

- [Popular](#populate-a-carouselpage-with-a-page-collection) uma `CarouselPage` com uma coleção de instancias filhas de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Atribuir](#populate-a-carouselpage-with-a-template) uma coleção à propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e atribuir um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para retornar instâncias de [`ContentPage`](xref:Xamarin.Forms.ContentPage) para objetos na coleção.

Com as duas abordagens, a `CarouselPage` exibirá uma página por vez, com uma interação de passar o dedo levando para a próxima página a ser exibida.

> [!NOTE]
> Um [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) só pode ser populado com [`ContentPage`](xref:Xamarin.Forms.ContentPage) instâncias ou `ContentPage` derivativos.

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

Cada uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) simplesmente exibe um [`Label`](xref:Xamarin.Forms.Label) para uma cor específica e uma [`BoxView`](xref:Xamarin.Forms.BoxView) dessas cores.

### <a name="populate-a-carouselpage-with-a-template"></a>Popular uma CarouselPage com um modelo

O exemplo de código XAML a seguir mostra um construído atribuindo [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade para retornar páginas para objetos na coleção:

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

O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é preenchido com dados definindo a [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade no construtor para o arquivo code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

O exemplo de código a seguir mostra o equivalente [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) criado em C#:

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

Cada uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) simplesmente exibe um [`Label`](xref:Xamarin.Forms.Label) para uma cor específica e uma [`BoxView`](xref:Xamarin.Forms.BoxView) dessas cores.

## <a name="related-links"></a>Links relacionados

- [Variedades de páginas](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
