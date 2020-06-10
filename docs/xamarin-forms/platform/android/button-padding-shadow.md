---
Título: "preenchimento de botão e sombras no Android" Descrição: "as especificações de plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que usa o preenchimento padrão e os valores de sombra dos botões do Android. "
MS. Prod: xamarin MS. AssetID: BD2B60D1-DE6E-4691-A777-8EC5F560A4E9 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="button-padding-and-shadows-on-android"></a>Preenchimento de botão e sombras no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android controla se Xamarin.Forms os botões usam o preenchimento padrão e os valores de sombra dos botões do Android. Ele é consumido em XAML definindo [`Button.UseDefaultPadding`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) as [`Button.UseDefaultShadow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) Propriedades anexadas e como `boolean` valores:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

O `Button.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `Button.SetUseDefaultPadding` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. SetUseDefaultPadding ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button}, System. Boolean)) e [ `Button.SetUseDefaultShadow` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. SetUseDefaultShadow ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Botões}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, são usadas para controlar se Xamarin.Forms os botões usam o preenchimento padrão e os valores de sombra dos botões do Android. Além disso, o [ `Button.UseDefaultPadding` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. UseDefaultPadding ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button})) e [ `Button.UseDefaultShadow` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. UseDefaultShadow ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button})), os métodos podem ser usados para retornar se um botão usa o valor de preenchimento padrão e o valor de sombra padrão, respectivamente.

O resultado é que os Xamarin.Forms botões podem usar o preenchimento padrão e os valores de sombra dos botões do Android:

![](button-padding-shadow-images/button-padding-and-shadow.png "Default Padding and Shadow Values on Android Buttons")

Observe que, na captura de tela acima [`Button`](xref:Xamarin.Forms.Button) , cada uma tem definições idênticas, exceto que o lado direito `Button` usa os valores de preenchimento e sombra padrão dos botões do Android.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
