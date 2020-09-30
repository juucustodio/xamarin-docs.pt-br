---
title: Xamarin.Forms ScrollView
description: O Xamarin.Forms ScrollView é um layout que é capaz de rolar seu conteúdo.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af8513d86b7cf02ec7adcee75241d8ce99e2dfab
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556822"
---
# <a name="no-locxamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![::: no-Loc (Xamarin. Forms)::: ScrollView](scrollview-images/layouts.png "::: no-Loc (Xamarin. Forms)::: ScrollView")](scrollview-images/layouts-large.png#lightbox "::: no-Loc (Xamarin. Forms)::: ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView) é um layout que é capaz de rolar seu conteúdo. A `ScrollView` classe deriva da [`Layout`](xref:Xamarin.Forms.Layout) classe e, por padrão, rola seu conteúdo verticalmente. Um `ScrollView` só pode ter um único filho, embora isso possa ser outros layouts.

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView) os objetos não devem ser aninhados. Além disso, `ScrollView` os objetos não devem ser aninhados com outros controles que fornecem rolagem, como [`CollectionView`](xref:Xamarin.Forms.CollectionView) , [`ListView`](xref:Xamarin.Forms.ListView) e [`WebView`](xref:Xamarin.Forms.WebView) .

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define as seguintes propriedades:

- [`Content`](xref:Xamarin.Forms.ScrollView.Content), do tipo [`View`](xref:Xamarin.Forms.View) , representa o conteúdo a ser exibido no [`ScrollView`](xref:Xamarin.Forms.ScrollView) .
- [`ContentSize`](xref:Xamarin.Forms.ScrollView), do tipo [`Size`](xref:Xamarin.Forms.Size) , representa o tamanho do conteúdo. Trata-se de uma propriedade somente leitura.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), do tipo [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , representa quando a barra de rolagem horizontal é visível.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation), do tipo [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , representa a direção da rolagem do [`ScrollView`](xref:Xamarin.Forms.ScrollView) . O valor padrão dessa propriedade é `Vertical`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX), do tipo `double` , indica a posição de rolagem X atual. O valor padrão dessa propriedade somente leitura é 0.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY), do tipo `double` , indica a posição de rolagem Y atual. O valor padrão dessa propriedade somente leitura é 0.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), do tipo [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , representa quando a barra de rolagem vertical é visível.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, com exceção da [`Content`](xref:Xamarin.Forms.ScrollView.Content) propriedade, o que significa que eles podem ser destinos de associações de dados e estilizados.

A [`Content`](xref:Xamarin.Forms.ScrollView.Content) propriedade é a [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) da [`ScrollView`](xref:Xamarin.Forms.ScrollView) classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

> [!TIP]
> Para obter o melhor desempenho de layout possível, siga as diretrizes em [otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="scrollview-as-a-root-layout"></a>ScrollView como um layout raiz

Um [`ScrollView`](xref:Xamarin.Forms.ScrollView) só pode ter um único filho, que pode ser outros layouts. Portanto, é comum que um seja `ScrollView` o layout raiz de uma página. Para rolar seu conteúdo filho, [`ScrollView`](xref:Xamarin.Forms.ScrollView) o computa a diferença entre a altura de seu conteúdo e sua própria altura. Essa diferença é o valor que o `ScrollView` pode rolar para seu conteúdo.

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) será geralmente o filho de um `ScrollView` . Nesse cenário, o faz com que `ScrollView` o seja `StackLayout` tão alto quanto a soma das alturas de seus filhos. Em seguida, o `ScrollView` pode determinar o valor que seu conteúdo pode ser rolado. Para obter mais informações sobre o `StackLayout` , consulte [ Xamarin.Forms StackLayout](stacklayout.md).

> [!CAUTION]
> Em uma vertical [`ScrollView`](xref:Xamarin.Forms.ScrollView) , evite definir a `VerticalOptions` propriedade como `Start` , `Center` ou `End` . Isso diz para que o seja `ScrollView` tão alto quanto precisa ser, o que poderia ser zero. Enquanto Xamarin.Forms protege contra essa eventualidade, é melhor evitar o código que sugere algo que você não quer que aconteça.

O exemplo de XAML a seguir tem um [`ScrollView`](xref:Xamarin.Forms.ScrollView) como um layout raiz em uma página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ScrollViewDemos"
             x:Class="ScrollViewDemos.Views.ColorListPage"
             Title="ScrollView demo">
    <ScrollView>
        <StackLayout BindableLayout.ItemsSource="{x:Static local:NamedColor.All}">
            <BindableLayout.ItemTemplate>
                <DataTemplate>
                    <StackLayout Orientation="Horizontal">
                        <BoxView Color="{Binding Color}"
                                 HeightRequest="32"
                                 WidthRequest="32"
                                 VerticalOptions="Center" />
                        <Label Text="{Binding FriendlyName}"
                               FontSize="24"
                               VerticalOptions="Center" />
                    </StackLayout>
                </DataTemplate>
            </BindableLayout.ItemTemplate>
        </StackLayout>
    </ScrollView>
</ContentPage>
```

Neste exemplo, o [`ScrollView`](xref:Xamarin.Forms.ScrollView) tem seu conteúdo definido como um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que usa um layout vinculável para exibir os [`Color`](xref:Xamarin.Forms.Color) campos definidos pelo Xamarin.Forms . Por padrão, uma `ScrollView` rola verticalmente, que revela mais conteúdo:

[![Captura de tela de um layout de ScrollView raiz](scrollview-images/root-layout.png "Layout de ScrollView raiz")](scrollview-images/root-layout-large.png#lightbox "Layout de ScrollView raiz")

Este é o código C# equivalente:

```csharp
public class ColorListPageCode : ContentPage
{
    public ColorListPageCode()
    {
        DataTemplate dataTemplate = new DataTemplate(() =>
        {
            BoxView boxView = new BoxView
            {
                HeightRequest = 32,
                WidthRequest = 32,
                VerticalOptions = LayoutOptions.Center
            };
            boxView.SetBinding(BoxView.ColorProperty, "Color");

            Label label = new Label
            {
                FontSize = 24,
                VerticalOptions = LayoutOptions.Center
            };
            label.SetBinding(Label.TextProperty, "FriendlyName");

            StackLayout horizontalStackLayout = new StackLayout
            {
                Orientation = StackOrientation.Horizontal,
                Children = { boxView, label }
            };
            return horizontalStackLayout;
        });

        StackLayout stackLayout = new StackLayout();
        BindableLayout.SetItemsSource(stackLayout, NamedColor.All);
        BindableLayout.SetItemTemplate(stackLayout, dataTemplate);

        ScrollView scrollView = new ScrollView { Content = stackLayout };

        Title = "ScrollView demo";
        Content = scrollView;
    }
}
```

Para obter mais informações sobre layouts vinculáveis, consulte [layouts Xamarin.Forms vinculáveis no ](bindable-layouts.md).

## <a name="scrollview-as-a-child-layout"></a>ScrollView como um layout filho

Um [`ScrollView`](xref:Xamarin.Forms.ScrollView) pode ser um layout filho para um layout pai diferente.

Um [`ScrollView`](xref:Xamarin.Forms.ScrollView) será geralmente o filho de um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Um `ScrollView` requer uma altura específica para computar a diferença entre a altura de seu conteúdo e sua própria altura, com a diferença sendo o valor que o `ScrollView` pode rolar para seu conteúdo. Quando um `ScrollView` é o filho de a `StackLayout` , ele não recebe uma altura específica. O `StackLayout` deseja que o seja o `ScrollView` mais curto possível, que é a altura do `ScrollView` conteúdo ou zero. Para lidar com esse cenário, a `VerticalOptions` propriedade de `ScrollView` deve ser definida como `FillAndExpand` . Isso fará com que o `StackLayout` forneça o `ScrollView` espaço extra não exigido pelos outros filhos, e o terá `ScrollView` uma altura específica.

O exemplo de XAML a seguir tem um [`ScrollView`](xref:Xamarin.Forms.ScrollView) como um layout filho para um [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ScrollViewDemos.Views.BlackCatPage"
             Title="ScrollView as a child layout demo">
    <StackLayout Margin="20">
        <Label Text="THE BLACK CAT by Edgar Allan Poe"
               FontSize="Medium"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <ScrollView VerticalOptions="FillAndExpand">
            <StackLayout>
                <Label Text="FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects." />
                <!-- More Label objects go here -->
            </StackLayout>
        </ScrollView>
    </StackLayout>
</ContentPage>
```

Neste exemplo, há dois [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos. O primeiro `StackLayout` é o objeto de layout raiz, que tem um [`Label`](xref:Xamarin.Forms.Label) objeto e um [`ScrollView`](xref:Xamarin.Forms.ScrollView) como seus filhos. O `ScrollView` tem um `StackLayout` como conteúdo, com o `StackLayout` que contém vários `Label` objetos. Essa organização garante que a primeira `Label` esteja sempre na tela, enquanto o texto exibido pelos outros `Label` objetos pode ser rolado:

[![Captura de tela de um layout de ScrollView filho](scrollview-images/child-layout.png "Layout de ScrollView filho")](scrollview-images/child-layout-large.png#lightbox "Layout de ScrollView filho")

Este é o código C# equivalente:

```csharp
public class BlackCatPageCS : ContentPage
{
    public BlackCatPageCS()
    {
        Label titleLabel = new Label
        {
            Text = "THE BLACK CAT by Edgar Allan Poe",
            // More properties set here to define the Label appearance
        };

        ScrollView scrollView = new ScrollView
        {
            VerticalOptions = LayoutOptions.FillAndExpand,
            Content = new StackLayout
            {
                Children =
                {
                    new Label
                    {
                        Text = "FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects.",
                    },
                    // More Label objects go here
                }
            }
        };

        Title = "ScrollView as a child layout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { titleLabel, scrollView }
        };
    }
}
```

## <a name="orientation"></a>Orientação

[`ScrollView`](xref:Xamarin.Forms.ScrollView) tem uma [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriedade, que representa a direção da rolagem do `ScrollView` . Essa propriedade é do tipo [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , que define os seguintes membros:

- `Vertical` indica que o `ScrollView` será rolado verticalmente. Esse membro é o valor padrão da [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriedade.
- `Horizontal` indica que o `ScrollView` irá rolar horizontalmente.
- `Both` indica que o `ScrollView` irá rolar horizontal e verticalmente.
- `Neither` indica que o `ScrollView` não rolará.

> [!TIP]
> A rolagem pode ser desabilitada definindo a [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propriedade como `Neither` .

## <a name="detect-scrolling"></a>Detectar rolagem

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define um [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento que é acionado para indicar que a rolagem ocorreu. O [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) objeto que acompanha o `Scrolled` evento tem `ScrollX` e `ScrollY` Propriedades, ambos os tipos `double` .

> [!IMPORTANT]
> As `ScrolledEventArgs.ScrollX` `ScrolledEventArgs.ScrollY` Propriedades e podem ter valores negativos, devido ao efeito de repercussão que ocorre ao rolar de volta para o início de um [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

O exemplo de XAML a seguir mostra um [`ScrollView`](xref:Xamarin.Forms.ScrollView) que define um manipulador de eventos para o [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento:

```xaml
<ScrollView Scrolled="OnScrollViewScrolled">
        ...
</ScrollView>
```

Este é o código C# equivalente:

```csharp
ScrollView scrollView = new ScrollView();
scrollView.Scrolled += OnScrollViewScrolled;
```

Neste exemplo, o `OnScrollViewScrolled` manipulador de eventos é executado quando o [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento é acionado:

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

Neste exemplo, o `OnScrollViewScrolled` manipulador de eventos gera os valores do [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) objeto que acompanha o evento.

> [!NOTE]
> O [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento é acionado para rolagens iniciadas pelo usuário e para rolagens programáticas.

## <a name="scroll-programmatically"></a>Rolar programaticamente

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define dois [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) métodos, que rolam de forma assíncrona o `ScrollView` . Uma das sobrecargas rola para uma posição especificada no `ScrollView` , enquanto a outra rola um elemento especificado para o modo de exibição. Ambas as sobrecargas têm um argumento adicional que pode ser usado para indicar se a rolagem deve ser animada.

> [!IMPORTANT]
> Os [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) métodos não resultarão na rolagem quando a [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propriedade for definida como `Neither` .

### <a name="scroll-a-position-into-view"></a>Rolar uma posição para a exibição

Uma posição dentro de um [`ScrollView`](xref:Xamarin.Forms.ScrollView) pode ser rolada com o [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) método que aceita `double` `x` `y` argumentos e. Dado um `ScrollView` objeto vertical chamado `scrollView` , o exemplo a seguir mostra como rolar até 150 unidades independentes do dispositivo na parte superior do `ScrollView` :

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

O terceiro argumento para [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) é o `animated` argumento, que determina se uma animação de rolagem é exibida ao rolar programaticamente um [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

### <a name="scroll-an-element-into-view"></a>Rolar um elemento para a exibição

Um elemento dentro de um [`ScrollView`](xref:Xamarin.Forms.ScrollView) pode ser rolado para a exibição com o [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) método que aceita [`Element`](xref:Xamarin.Forms.Element) e [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) argumentos. Considerando um `ScrollView` nome vertical `scrollView` e um [`Label`](xref:Xamarin.Forms.Label) nomeado `label` , o exemplo a seguir mostra como rolar um elemento para a exibição:

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

O terceiro argumento para [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) é o `animated` argumento, que determina se uma animação de rolagem é exibida ao rolar programaticamente um [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Ao rolar um elemento para a exibição, a posição exata do elemento após a rolagem concluída pode ser definida com o segundo argumento, `position` , do [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) método. Esse argumento aceita um [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro de enumeração:

- `MakeVisible` indica que o elemento deve ser rolado até que esteja visível no `ScrollView` .
- `Start` indica que o elemento deve ser rolado para o início do `ScrollView` .
- `Center` indica que o elemento deve ser rolado para o centro do `ScrollView` .
- `End` indica que o elemento deve ser rolado para o final do `ScrollView` .

## <a name="scroll-bar-visibility"></a>Visibilidade da barra de rolagem

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) e [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) Propriedades, que são apoiadas por propriedades vinculáveis. Essas propriedades obtêm ou definem um [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) valor de enumeração que representa se a barra de rolagem horizontal ou vertical está visível. A enumeração `ScrollBarVisibility` define os seguintes membros:

- `Default` indica o comportamento da barra de rolagem padrão para a plataforma e é o valor padrão `HorizontalScrollBarVisibility` das `VerticalScrollBarVisibility` Propriedades e.
- `Always` indica que as barras de rolagem estarão visíveis, mesmo quando o conteúdo couber na exibição.
- `Never` indica que as barras de rolagem não estarão visíveis, mesmo se o conteúdo não couber na exibição.

## <a name="related-links"></a>Links relacionados

- [Demonstrações do ScrollView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [Layouts vinculáveis em Xamarin.Forms](bindable-layouts.md)