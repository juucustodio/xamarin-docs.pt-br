---
Título: "modo de transição do SwipeView do dedo no iOS" Descrição: "as especificações da plataforma permitem que você consuma a funcionalidade disponível apenas em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que controla a transição usada ao abrir um SwipeView. "
MS. Prod: xamarin MS. AssetID: C667F24C-BAD8-47E0-9285-D3546BEF703B MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/11/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="swipeview-swipe-transition-mode-on-ios"></a>Modo de transição de toque SwipeView no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica controla a transição que é usada ao abrir um `SwipeView` . Ele é consumido em XAML definindo a `SwipeView.SwipeTransitionMode` propriedade vinculável como um valor da `SwipeTransitionMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

O `SwipeView.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `SwipeView.SetSwipeTransitionMode` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar a transição usada ao abrir um `SwipeView` . A `SwipeTransitionMode` enumeração fornece dois valores possíveis:

- `Reveal`indica que os itens do dedo serão revelados à medida que o `SwipeView` conteúdo é transdedodo e é o valor padrão da `SwipeView.SwipeTransitionMode` propriedade.
- `Drag`indica que os itens do dedo serão arrastados para a exibição à medida que o `SwipeView` conteúdo é transdedodo.

Além disso, o `SwipeView.GetSwipeTransitionMode` método pode ser usado para retornar o `SwipeTransitionMode` que é aplicado ao `SwipeView` .

O resultado é que um `SwipeTransitionMode` valor especificado é aplicado ao `SwipeView` , que controla a transição usada ao abrir o `SwipeView` :

[![Captura de tela de SwipeView SwipeTransitionModes, no iOS](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes no iOS")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes no iOS")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
