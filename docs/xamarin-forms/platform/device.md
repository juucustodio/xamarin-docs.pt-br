---
title: Classe de dispositivo do xamarin. Forms
description: Este artigo explica como usar a classe de dispositivo do xamarin. Forms, o controle refinado sobre layouts e funcionalidade em uma base por plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 25ddbea75d0fd6858f848499281da5d5f0b68171
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305321"
---
# <a name="xamarinforms-device-class"></a>Classe de dispositivo do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

A classe [`Device`](xref:Xamarin.Forms.Device) contém uma série de propriedades e métodos para ajudar os desenvolvedores a personalizar o layout e a funcionalidade de acordo com a plataforma.

Além de métodos e propriedades para direcionar o código em tipos e tamanhos de hardware específicos, a classe `Device` inclui métodos que podem ser usados para interagir com controles de interface do usuário de threads em segundo plano. Para obter mais informações, consulte [interagir com a interface do usuário de threads em segundo plano](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Fornecendo valores específicos da plataforma

Antes do Xamarin. Forms 2.3.4, a plataforma em que o aplicativo estava sendo executado poderia ser obtida examinando a propriedade [`Device.OS`](xref:Xamarin.Forms.Device.OS) e comparando-a com os valores de enumeração [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS), [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android), [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)e [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) . Da mesma forma, uma das sobrecargas de [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) poderia ser usada para fornecer valores específicos da plataforma a um controle.

No entanto, desde o xamarin. Forms 2.3.4 essas APIs foram preteridas e substituídas. A classe [`Device`](xref:Xamarin.Forms.Device) agora contém constantes de cadeia de caracteres públicas que identificam plataformas – [`Device.iOS`](xref:Xamarin.Forms.Device.iOS), [`Device.Android`](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`(preterido), `Device.WinRT` (preterido), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)e [`Device.macOS`](xref:Xamarin.Forms.Device.macOS). Da mesma forma, as sobrecargas de [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) foram substituídas pelas APIs [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) .

No C#, os valores específicos da plataforma podem ser fornecidos criando uma instrução `switch` na propriedade [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) e, em seguida, fornecendo `case` instruções para as plataformas necessárias:

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

As classes [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) fornecem a mesma funcionalidade em XAML:

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

A classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) é uma classe genérica que deve ser instanciada com um atributo `x:TypeArguments` que corresponda ao tipo de destino. Na classe [`On`](xref:Xamarin.Forms.On) , o atributo [`Platform`](xref:Xamarin.Forms.On.Platform) pode aceitar um único valor de `string` ou vários valores de `string` delimitados por vírgula.

> [!IMPORTANT]
> Fornecer um valor de atributo de `Platform` incorreto na classe `On` não resultará em um erro. Em vez disso, o código será executado sem o valor específico da plataforma que está sendo aplicado.

Como alternativa, a extensão de marcação `OnPlatform` pode ser usada em XAML para personalizar a aparência da interface do usuário em uma base por plataforma. Para obter mais informações, consulte [extensão de marcação do onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Device.Idiom

A propriedade `Device.Idiom` pode ser usada para alterar layouts ou funcionalidades dependendo do dispositivo em que o aplicativo está sendo executado. A enumeração de [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) contém os seguintes valores:

- **Telefone** – iPhone, iPod Touch e dispositivos Android mais estreitos que 600% DIPs
- **Tablet** – iPad, dispositivos Windows e dispositivos Android mais largos que 600% DIPs
- **Área de trabalho** – retornada somente em [aplicativos UWP](~/xamarin-forms/platform/windows/installation/index.md) em computadores desktop com windows 10 (retorna `Phone` em dispositivos móveis do Windows, incluindo em cenários de continuidade)
- **TV** – dispositivos de TV tizen
- **Assista** – dispositivos tizen Watch
- **Sem suporte** – não utilizado

*^ DIPs não é necessariamente a contagem de pixels físicos*

A propriedade `Idiom` é especialmente útil para criar layouts que aproveitam telas maiores, como esta:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

A classe [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) fornece a mesma funcionalidade em XAML:

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

A classe [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) é uma classe genérica que deve ser instanciada com um atributo `x:TypeArguments` que corresponda ao tipo de destino.

Como alternativa, a extensão de marcação de `OnIdiom` pode ser usada em XAML para personalizar a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado. Para obter mais informações, consulte [extensão de marcação no idioma](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device.FlowDirection

O valor de [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) recupera um valor de enumeração de [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) que representa a direção de fluxo atual que está sendo usada pelo dispositivo. A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. Os valores de enumeração são:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Em XAML, o valor de [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) pode ser recuperado usando a extensão de marcação `x:Static`:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

O código equivalente no C# é:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device.Styles

A [propriedade`Styles`](~/xamarin-forms/user-interface/styles/index.md) contém definições de estilo interno que podem ser aplicadas a alguns controles (como `Label`) `Style` propriedade. Os estilos disponíveis são:

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` pode ser usado ao definir [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) no C# código:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicestarttimer"></a>Device.StartTimer

A classe `Device` também tem um método `StartTimer` que fornece uma maneira simples de disparar tarefas dependentes de tempo que funcionam no código comum do Xamarin. Forms, incluindo uma biblioteca .NET Standard. Passe um `TimeSpan` para definir o intervalo e retornar `true` para manter o temporizador em execução ou `false` para interrompê-lo após a invocação atual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se o código dentro do temporizador interage com a interface do usuário (como definir o texto de um `Label` ou exibir um alerta), ele deve ser feito dentro de uma expressão de `BeginInvokeOnMainThread` (veja abaixo).

> [!NOTE]
> As classes `System.Timers.Timer` e `System.Threading.Timer` são .NET Standard alternativas ao uso do método `Device.StartTimer`.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir com a interface do usuário a partir de threads em segundo plano

A maioria dos sistemas operacionais, incluindo iOS, Android e o Plataforma Universal do Windows, usa um modelo de thread único para o código que envolve a interface do usuário. Esse thread geralmente é chamado de *thread principal* ou *thread de interface do usuário*. Uma consequência desse modelo é que todo o código que acessa elementos da interface do usuário deve ser executado no thread principal do aplicativo.

Os aplicativos às vezes usam threads em segundo plano para executar operações potencialmente demoradas, como recuperar dados de um serviço Web. Se o código em execução em um thread em segundo plano precisar acessar elementos da interface do usuário, ele deverá executar esse código no thread principal.

A classe `Device` inclui os seguintes métodos `static` que podem ser usados para interagir com elementos de interface do usuário de threads de planos de fundo:

| Método | Argumentos | Retorna | Finalidade |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Invoca uma `Action` no thread principal e não aguarda sua conclusão. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca um `Func<T>` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca um `Action` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca um `Func<Task<T>>` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca um `Func<Task>` no thread principal e aguarda sua conclusão. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retorna o `SynchronizationContext` para o thread principal. |

O código a seguir mostra um exemplo de como usar o método `BeginInvokeOnMainThread`:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de dispositivo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Exemplo de estilos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Dispositivo](xref:Xamarin.Forms.Device)
