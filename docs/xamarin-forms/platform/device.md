---
title: Classe de dispositivo Xamarin. Forms
description: Este artigo explica como usar a classe de dispositivo Xamarin. Forms, para um controle refinado sobre a funcionalidade e os layouts de acordo com a plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
ms.openlocfilehash: d0f0fa7dd68e8852dd7a72486c155ec064540644
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517065"
---
# <a name="xamarinforms-device-class"></a>Classe de dispositivo Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

A [`Device`](xref:Xamarin.Forms.Device) classe contém várias propriedades e métodos para ajudar os desenvolvedores a personalizar o layout e a funcionalidade de acordo com a plataforma.

Além de métodos e propriedades para direcionar o código em tipos e tamanhos de hardware específicos `Device` , a classe inclui métodos que podem ser usados para interagir com controles de interface do usuário de threads em segundo plano. Para obter mais informações, consulte [interagir com a interface do usuário de threads em segundo plano](#interact-with-the-ui-from-background-threads).

## <a name="provide-platform-specific-values"></a>Fornecer valores específicos da plataforma

Antes do Xamarin. Forms 2.3.4, a plataforma em que o aplicativo estava sendo executado poderia ser obtida [`Device.OS`](xref:Xamarin.Forms.Device.OS) examinando a propriedade e comparando-a com os valores de [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)enumeração, [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android) [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone), e. [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) Da mesma forma, uma [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) das sobrecargas pode ser usada para fornecer valores específicos da plataforma a um controle.

No entanto, como o Xamarin. Forms 2.3.4 essas APIs foram preteridas e substituídas. A [`Device`](xref:Xamarin.Forms.Device) classe agora contém constantes de cadeia de caracteres públicas que [`Device.iOS`](xref:Xamarin.Forms.Device.iOS)identificam plataformas – [`Device.Android`](xref:Xamarin.Forms.Device.Android),, `Device.WinPhone`( `Device.WinRT` preterido), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)(preterido), e [`Device.macOS`](xref:Xamarin.Forms.Device.macOS). Da mesma forma [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) , as sobrecargas foram substituídas [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) pelas [`On`](xref:Xamarin.Forms.On) APIs e.

Em C#, os valores específicos da plataforma podem ser fornecidos criando uma `switch` instrução na [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) Propriedade e, em seguida, `case` fornecendo instruções para as plataformas necessárias:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

As [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classes [`On`](xref:Xamarin.Forms.On) e fornecem a mesma funcionalidade em XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

A [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe é uma classe genérica que deve ser instanciada com um `x:TypeArguments` atributo que corresponde ao tipo de destino. Na [`On`](xref:Xamarin.Forms.On) classe, o [`Platform`](xref:Xamarin.Forms.On.Platform) atributo pode aceitar um único `string` valor ou vários valores delimitados por `string` vírgula.

> [!IMPORTANT]
> Fornecer um valor `Platform` de atributo incorreto `On` na classe não resultará em um erro. Em vez disso, o código será executado sem o valor específico da plataforma que está sendo aplicado.

Como alternativa, a `OnPlatform` extensão de marcação pode ser usada em XAML para personalizar a aparência da interface do usuário em uma base por plataforma. Para obter mais informações, consulte [extensão de marcação do onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Dispositivo. idioma

A `Device.Idiom` propriedade pode ser usada para alterar layouts ou funcionalidades dependendo do dispositivo em que o aplicativo está sendo executado. A [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) enumeração contém os seguintes valores:

- **Telefone** – iPhone, iPod Touch e dispositivos Android mais estreitos que 600% DIPs
- **Tablet** – iPad, dispositivos Windows e dispositivos Android mais largos que 600% DIPs
- **Área de trabalho** – retornada somente em [aplicativos UWP](~/xamarin-forms/platform/windows/installation/index.md) em computadores desktop com Windows `Phone` 10 (retornos em dispositivos móveis do Windows, incluindo em cenários de continuidade)
- **TV** – dispositivos de TV tizen
- **Assista** – dispositivos tizen Watch
- **Sem suporte** – não utilizado

*^ DIPs não é necessariamente a contagem de pixels físicos*

A `Idiom` propriedade é especialmente útil para criar layouts que aproveitam telas maiores, como esta:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

A [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe fornece a mesma funcionalidade em XAML:

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

A [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) classe é uma classe genérica que deve ser instanciada com um `x:TypeArguments` atributo que corresponde ao tipo de destino.

Como alternativa, a `OnIdiom` extensão de marcação pode ser usada em XAML para personalizar a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado. Para obter mais informações, consulte [extensão de marcação no idioma](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device. FlowDirection

O [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valor recupera um [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valor de enumeração que representa a direção de fluxo atual que está sendo usada pelo dispositivo. A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. Os valores de enumeração são:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Em XAML, o [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valor pode ser recuperado usando a extensão `x:Static` de marcação:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

O código equivalente em C# é:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. Styles

A [ `Styles` Propriedade](~/xamarin-forms/user-interface/styles/index.md) contém definições de estilo interno que podem ser aplicadas a alguns controles (como `Label`) `Style` propriedade. Os estilos disponíveis são:

- BodyStyle
- Legendastyle
- ListItemDetailTextStyle
- ListItemTextStyle
- Subtítulostyle
- Títulostyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

`GetNamedSize`pode ser usado ao configurar [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) em código C#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Device. GetNamedColor

O Xamarin. Forms 4,6 apresenta suporte para cores nomeadas. Uma cor nomeada é uma cor que tem um valor diferente, dependendo de qual modo do sistema (por exemplo, claro ou escuro) está ativo no dispositivo. No Android, as cores nomeadas são acessadas por meio da classe [R. Color](https://developer.android.com/reference/android/R.color#constants_2) . No iOS, as cores nomeadas são chamadas de [cores do sistema](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors). Na Plataforma Universal do Windows, as cores nomeadas são chamadas de [recursos de tema XAML](/windows/uwp/design/controls-and-patterns/xaml-theme-resources).

O `GetNamedColor` método pode ser usado para recuperar cores nomeadas no Android, Ios e UWP. O método usa um `string` argumento e retorna um [`Color`](xref:Xamarin.Forms.Color):

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default`será retornado quando um nome de cor não puder ser encontrado ou quando `GetNamedColor` for invocado em uma plataforma sem suporte.

> [!NOTE]
> Como o `GetNamedColor` método retorna um `Color` que é específico para uma plataforma, ele normalmente deve ser usado em conjunto com a [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propriedade.

A `NamedPlatformColor` classe contém as constantes que definem as cores nomeadas para Android, Ios e UWP:

| Android | iOS | UWP |
| --- | --- | --- |
| `BackgroundDark` | `Label` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `SystemChromeDisabledHighColor` |
| `White` | `SystemListLowColor` | `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemListMediumColor` | `SystemChromeHighColor` |
| | `SystemPink` | `SystemChromeLowColor` |
| | `SystemPurple` | `SystemChromeMediumColor` |
| | `SystemRed` | `SystemChromeMediumLowColor` |
| | `SystemTeal` | `SystemChromeWhiteColor` |
| | `SystemYellow` |
| | `TertiaryLabel` |

## <a name="devicestarttimer"></a>Device. StartTimer

A `Device` classe também tem um `StartTimer` método que fornece uma maneira simples de disparar tarefas dependentes de tempo que funcionam no código comum do Xamarin. Forms, incluindo uma biblioteca de .net Standard. Passe um `TimeSpan` para definir o intervalo e retornar `true` para manter o temporizador em `false` execução ou para interrompê-lo após a invocação atual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se o código dentro do temporizador interage com a interface do usuário (como definir o texto de um `Label` ou exibir um alerta), ele deve ser feito dentro de `BeginInvokeOnMainThread` uma expressão (veja abaixo).

> [!NOTE]
> As `System.Timers.Timer` classes `System.Threading.Timer` e são .net Standard alternativas ao uso do `Device.StartTimer` método.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir com a interface do usuário a partir de threads em segundo plano

A maioria dos sistemas operacionais, incluindo iOS, Android e o Plataforma Universal do Windows, usa um modelo de thread único para o código que envolve a interface do usuário. Esse thread geralmente é chamado de *thread principal* ou *thread de interface do usuário*. Uma consequência desse modelo é que todo o código que acessa elementos da interface do usuário deve ser executado no thread principal do aplicativo.

Os aplicativos às vezes usam threads em segundo plano para executar operações potencialmente demoradas, como recuperar dados de um serviço Web. Se o código em execução em um thread em segundo plano precisar acessar elementos da interface do usuário, ele deverá executar esse código no thread principal.

A `Device` classe inclui os seguintes `static` métodos que podem ser usados para interagir com elementos de interface do usuário de threads de planos de fundo:

| Método | Argumentos | Retornos | Finalidade |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Invoca um `Action` no thread principal e não aguarda que ele seja concluído. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca um `Func<T>` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca um `Action` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca um `Func<Task<T>>` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca um `Func<Task>` no thread principal e aguarda sua conclusão. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retorna o `SynchronizationContext` para o thread principal. |

O código a seguir mostra um exemplo de como `BeginInvokeOnMainThread` usar o método:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de dispositivo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Exemplo de estilos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [API do dispositivo](xref:Xamarin.Forms.Device)
