---
Título: "modo de entrada de teclado virtual no Android" Descrição: "as especificações de plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que define o modo de operação para uma área de entrada de teclado virtual.
MS. Prod: xamarin MS. AssetID: AFCDC92F-F61E-42F6-904B-50B5C4949970 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="soft-keyboard-input-mode-on-android"></a>Modo de entrada de teclado virtual no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para definir o modo de operação para uma área de entrada de teclado virtual e é consumida em XAML definindo a [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) Propriedade anexada como um valor da [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeração:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

O `Application.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `Application.UseWindowSoftInputModeAdjust` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Application. UseWindowSoftInputModeAdjust ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Aplicativo}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir o modo de operação da área de entrada do teclado virtual, com a [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) Enumeração fornecendo dois valores: [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) e [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) . O `Pan` valor usa a [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) opção de ajuste, que não redimensiona a janela quando um controle de entrada tem foco. Em vez disso, o conteúdo da janela é panorâmico para que o foco atual não seja obscurecido pelo teclado virtual. O `Resize` valor usa a [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) opção de ajuste, que redimensiona a janela quando um controle de entrada tem foco, para liberar espaço para o teclado virtual.

O resultado é que o modo operacional da área de entrada do teclado virtual pode ser definido quando um controle de entrada tem foco:

[![](soft-keyboard-input-mode-images/pan-resize.png "Soft Keyboard Operating Mode Platform-Specific")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
