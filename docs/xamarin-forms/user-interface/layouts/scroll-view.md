---
Título: ' Xamarin.Forms ScrollView ' Descrição: ' Este artigo explica como usar a Xamarin.Forms classe ScrollView para apresentar layouts que não cabem em apenas uma tela e que têm espaço para o teclado. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)contém layouts e permite que eles rolem fora da tela. `ScrollView`também é usado para permitir que as exibições se movam automaticamente para a parte visível da tela quando o teclado está sendo exibido.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Finalidade

[`ScrollView`](xref:Xamarin.Forms.ScrollView)pode ser usado para garantir que exibições maiores sejam bem exibidas em telefones menores. Por exemplo, um layout que funciona em um iPhone 6s pode ser recortado em um 4s de iPhone. O uso de um `ScrollView` permitiria que as partes recortadas do layout fossem exibidas na tela menor.

## <a name="usage"></a>Uso

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)os objetos não devem ser aninhados. Além disso, os `ScrollView` s não devem ser aninhados com outros controles que fornecem rolagem, como `ListView` e `WebView` .

[`ScrollView`](xref:Xamarin.Forms.ScrollView)expõe uma `Content` propriedade, que pode ser definida como um único modo de exibição ou layout. Considere este exemplo de um layout com um boxView muito grande, seguido por um `Entry` :

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

No C#:

```csharp
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Antes que o usuário Role para baixo, somente o `BoxView` está visível:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Observe que, quando o usuário começa a inserir texto no `Entry` , a exibição rola para mantê-lo visível na tela:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propriedades

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define as seguintes propriedades:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)Obtém um [`Size`](xref:Xamarin.Forms.Size) valor que representa o tamanho do conteúdo.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)Obtém ou define um [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) valor de enumeração que representa a direção da rolagem do `ScrollView` .
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)Obtém um `double` que representa a posição de rolagem X atual.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)Obtém um `double` que representa a posição de rolagem Y atual.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)Obtém ou define um [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa quando a barra de rolagem horizontal é visível.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)Obtém ou define um [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa quando a barra de rolagem vertical é visível.

> [!NOTE]
> A rolagem pode ser desabilitada definindo a [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propriedade como `Neither` .

## <a name="methods"></a>Métodos

[`ScrollView`](xref:Xamarin.Forms.ScrollView)fornece um `ScrollToAsync` método, que pode ser usado para rolar a exibição usando coordenadas ou especificando uma exibição específica que deve se tornar visível.

Ao usar coordenadas, especifique as `x` `y` coordenadas e, junto com um booliano que indica se a rolagem deve ser animada:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> O `ScrollToAsync` método não fará a rolagem quando a [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propriedade for definida como `Neither` .

Ao rolar para um elemento específico, a `ScrollToPosition` enumeração especifica onde o elemento aparecerá:

- **Central** &ndash; rola o elemento para o centro da parte visível da exibição.
- **Fim** &ndash; rola o elemento para o final da parte visível da exibição.
- **MakeVisible** &ndash; rola o elemento para que ele fique visível na exibição.
- **Iniciar** &ndash; rola o elemento para o início da parte visível da exibição.

A `IsAnimated` propriedade especifica como a exibição será rolada. Quando definido como `true` , uma animação suave será usada, em vez de mover instantaneamente o conteúdo para a exibição.

## <a name="events"></a>Eventos

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define apenas um evento, `Scrolled` . `Scrolled`é gerado quando a exibição termina de rolagem. O manipulador de eventos `Scrolled` para `ScrolledEventArgs` o usa, que tem as `ScrollX` `ScrollY` Propriedades e. O seguinte demonstra como atualizar um rótulo com a posição de rolagem atual de um `ScrollView` :

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Observe que as posições de rolagem podem ser negativas, devido ao efeito de repercussão durante a rolagem no final de uma lista.

## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
