---
Título: "criar um Xamarin.Forms processador visual" Descrição: "criar Xamarin.Forms visuais para serem aplicados seletivamente a objetos visualelement, sem precisar de Xamarin.Forms exibições de subclasse".
MS. Prod: xamarin MS. AssetID: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 03/12/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="create-a-xamarinforms-visual-renderer"></a>Criar um Xamarin.Forms renderizador Visual

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Xamarin.FormsO Visual permite que os renderizadores sejam criados e aplicados seletivamente a [`VisualElement`](xref:Xamarin.Forms.VisualElement) objetos, sem a necessidade de Xamarin.Forms exibições de subclasse. Um renderizador que especifica um `IVisual` tipo, como parte de seu `ExportRendererAttribute` , será usado para renderizar os modos de exibição, em vez do renderizador padrão. No momento da seleção do processador, a `Visual` propriedade da exibição é inspecionada e incluída no processo de seleção do processador.

> [!IMPORTANT]
> Atualmente [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) , a propriedade não pode ser alterada depois que a exibição tiver sido renderizada, mas isso será alterado em uma versão futura.

O processo para criar e consumir um Xamarin.Forms renderizador Visual é:

1. Crie renderizadores de plataforma para a exibição necessária. Para obter mais informações, consulte [criar renderizadores](#create-platform-renderers).
1. Crie um tipo derivado de `IVisual` . Para obter mais informações, consulte [criar um tipo de IVisual](#create-an-ivisual-type).
1. Registre o `IVisual` tipo como parte do `ExportRendererAttribute` que decora os renderizadores. Para obter mais informações, consulte [registrar o tipo IVisual](#register-the-ivisual-type).
1. Consuma o renderizador Visual definindo a [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) Propriedade na exibição como o `IVisual` nome. Para obter mais informações, consulte [consumir o processador visual](#consume-the-visual-renderer).
1. adicional Registre um nome para o `IVisual` tipo. Para obter mais informações, consulte [registrar um nome para o tipo IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Criar renderizadores de plataforma

Para obter informações sobre como criar uma classe de processador, consulte [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). No entanto, observe que um Xamarin.Forms renderizador Visual é aplicado a uma exibição sem a necessidade de subclasse da exibição.

As classes de processador descritas aqui implementam um personalizado [`Button`](xref:Xamarin.Forms.Button) que exibe seu texto com uma sombra.

### <a name="ios"></a>iOS

O exemplo de código a seguir mostra o renderizador de botão para iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

O exemplo de código a seguir mostra o renderizador de botão para Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Criar um tipo de IVisual

Em sua biblioteca de plataforma cruzada, crie um tipo que derive de `IVisual` :

```csharp
public class CustomVisual : IVisual
{
}
```

O `CustomVisual` tipo pode então ser registrado em relação às classes do processador, permitindo [`Button`](xref:Xamarin.Forms.Button) que os objetos optem por usar os renderizadores.

## <a name="register-the-ivisual-type"></a>Registrar o tipo IVisual

Nos projetos da plataforma, adicione o `ExportRendererAttribute` no nível do assembly:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

Neste exemplo para o projeto da plataforma iOS, `ExportRendererAttribute` especifica que a `CustomButtonRenderer` classe será usada para renderizar objetos consumidos [`Button`](xref:Xamarin.Forms.Button) , com o `IVisual` tipo registrado como o terceiro argumento. Um renderizador que especifica um `IVisual` tipo, como parte de seu `ExportRendererAttribute` , será usado para renderizar os modos de exibição, em vez do renderizador padrão.

## <a name="consume-the-visual-renderer"></a>Consumir o processador Visual

Um [`Button`](xref:Xamarin.Forms.Button) objeto pode optar por usar as classes do processador definindo sua [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propriedade como `Custom` :

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> Em XAML, um conversor de tipo remove a necessidade de incluir o sufixo "Visual" no [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) valor da propriedade. No entanto, o nome completo do tipo também pode ser especificado.

Este é o código C# equivalente:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

No momento da seleção do processador, a [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) Propriedade do [`Button`](xref:Xamarin.Forms.Button) é inspecionada e incluída no processo de seleção do processador. Se um renderizador não estiver localizado, o Xamarin.Forms renderizador padrão será usado.

As capturas de tela a seguir mostram o renderizado [`Button`](xref:Xamarin.Forms.Button) , que exibe o texto com uma sombra:

[![Captura de tela do botão personalizado com texto sombreado no iOS e no Android](material-visual-images/custom-button.png "Botão com texto de sombra")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registrar um nome para o tipo IVisual

O [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) pode ser usado para, opcionalmente, registrar um nome diferente para o `IVisual` tipo. Essa abordagem pode ser usada para resolver conflitos de nomenclatura entre diferentes bibliotecas visuais, ou em situações em que você queira apenas fazer referência a um Visual por um nome diferente do nome do tipo.

O [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) deve ser definido no nível do assembly na biblioteca de plataforma cruzada ou no projeto da plataforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

O `IVisual` tipo pode então ser consumido por meio de seu nome registrado:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Ao consumir um Visual por meio de seu nome registrado, qualquer sufixo "Visual" deve ser incluído.

## <a name="related-links"></a>Links relacionados

- [Visual do material (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Xamarin.FormsVisual do material](material-visual.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
