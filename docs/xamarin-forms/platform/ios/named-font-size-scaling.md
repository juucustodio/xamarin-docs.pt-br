---
Título: "dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS" Descrição: "as especificações de plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que desabilita o dimensionamento de acessibilidade para tamanhos de fonte nomeados.
MS. Prod: xamarin MS. AssetID: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 06/28/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica desabilita o dimensionamento de acessibilidade para tamanhos de fonte nomeados. Ele é consumido em XAML definindo a `Application.EnableAccessibilityScalingForNamedFontSizes` propriedade vinculável como `false` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

O `Application.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Application.SetEnableAccessibilityScalingForNamedFontSizes` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para desabilitar os tamanhos de fonte nomeados que estão sendo dimensionados pelas configurações de acessibilidade do Ios. Além disso, o `Application.GetEnableAccessibilityScalingForNamedFontSizes` método pode ser usado para retornar se os tamanhos de fonte nomeados são dimensionados pelas configurações de acessibilidade do Ios.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
