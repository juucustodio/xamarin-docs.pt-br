---
title: Classe de dispositivo do xamarin. Forms
description: Este artigo explica como usar a classe de dispositivo do xamarin. Forms, o controle refinado sobre layouts e funcionalidade em uma base por plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: eb1358f039cc5d5a200f929fcc7dfa71ca863d2a
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121310"
---
# <a name="xamarinforms-device-class"></a>Classe de dispositivo do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

O [ `Device` ](xref:Xamarin.Forms.Device) classe contém um número de propriedades e métodos para ajudar os desenvolvedores personalizar o layout e a funcionalidade em uma base por plataforma.

Além de métodos e propriedades para direcionar o código em tipos e tamanhos de hardware específicos `Device` , a classe inclui métodos que podem ser usados para interagir com controles de interface do usuário de threads em segundo plano. Para obter mais informações, consulte [interagir com a interface do usuário de threads em segundo plano](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Fornecendo valores específicos da plataforma

Antes do xamarin. Forms 2.3.4, a plataforma que o aplicativo estava em execução no poderia ser obtida examinando os [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) propriedade e compará-lo para o [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), e [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows) valores de enumeração. Da mesma forma, um dos [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sobrecargas pode ser usadas para fornecer valores específicos da plataforma para um controle.

No entanto, desde o xamarin. Forms 2.3.4 essas APIs foram preteridas e substituídas. O [ `Device` ](xref:Xamarin.Forms.Device) classe agora contém constantes de cadeia de caracteres pública que identificam as plataformas – [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`( preterido), `Device.WinRT` (preterido) [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), e [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). Da mesma forma, o [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sobrecargas foram substituídas com o [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) e [ `On` ](xref:Xamarin.Forms.On) APIs.

No C#, valores específicos de plataforma podem ser fornecidos com a criação de um `switch` instrução sobre o [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) propriedade e, em seguida, fornecendo `case` instruções para as plataformas necessárias:

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

O [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) e [ `On` ](xref:Xamarin.Forms.On) classes fornecem a mesma funcionalidade em XAML:

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

O [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) é uma classe genérica que deve ser instanciada com um `x:TypeArguments` atributo que corresponde ao tipo de destino. No [ `On` ](xref:Xamarin.Forms.On) classe, o [ `Platform` ](xref:Xamarin.Forms.On.Platform) atributo pode aceitar um único `string` valor ou vários delimitada por vírgula `string` valores.

> [!IMPORTANT]
> Fornecendo um incorreto `Platform` valor no atributo de `On` classe não resultará em erro. Em vez disso, o código será executado sem o valor específico da plataforma que está sendo aplicado.

Como alternativa, o `OnPlatform` extensão de marcação pode ser usada em XAML para personalizar a aparência da interface do usuário em uma base por plataforma. Para obter mais informações, consulte [extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Device.Idiom

O `Device.Idiom` propriedade pode ser usada para alterar os layouts ou funcionalidade dependendo do dispositivo em que o aplicativo está em execução no. O [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) enumeração contém os seguintes valores:

- **Telefone** – iPhone, iPod touch e dispositivos Android mais estreitos do que 600 quedas ^
- **Tablet** – iPad, dispositivos Windows e dispositivos Android mais de 600 quedas ^
- **Área de trabalho** – somente retornado no [aplicativos da UWP](~/xamarin-forms/platform/windows/installation/index.md) em computadores desktop do Windows 10 (retorna `Phone` em dispositivos móveis do Windows, incluindo em cenários de continuidade)
- **TV** – dispositivos Tizen TV
- **Assista** – Tizen dispositivos de inspeção
- **Não há suporte para** – não utilizados

*^ dips não é necessariamente a contagem de pixel físico*

O `Idiom` propriedade é especialmente útil para a criação de layouts que tiram proveito de telas maiores, como este:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

O [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) classe fornece a mesma funcionalidade em XAML:

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

O [ `OnIdiom` ](xref:Xamarin.Forms.OnPlatform`1) é uma classe genérica que deve ser instanciada com um `x:TypeArguments` atributo que corresponde ao tipo de destino.

Como alternativa, o `OnIdiom` extensão de marcação pode ser usada em XAML para personalizar a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado. Para obter mais informações, consulte [extensão de marcação OnIdiom](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device.FlowDirection

O [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor recupera uma [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valor de enumeração que representa a direção do fluxo atual que está sendo usada pelo dispositivo. A direção do fluxo é a direção na qual os elementos de interface do usuário na página são detectados pelos olhos. Os valores de enumeração são:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

No XAML, o [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor pode ser recuperado usando o `x:Static` extensão de marcação:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

O código equivalente no C# é:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device.Styles

O [ `Styles` propriedade](~/xamarin-forms/user-interface/styles/index.md) contém as definições de estilo interno que podem ser aplicadas a alguns dos controles (como `Label`) `Style` propriedade. Os estilos disponíveis são:

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` pode ser usado ao definir [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) em C# código:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="deviceopenuri"></a>Device.OpenUri

O `OpenUri` método pode ser usado para disparar operações na plataforma subjacente, como abrir uma URL no navegador da web nativos (**Safari** no iOS ou **Internet** no Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

O [amostra WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) inclui um exemplo do uso `OpenUri` para abrir URLs e também disparar chamadas telefônicas.

O [exemplo de mapas](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) também usa `Device.OpenUri` para exibir mapas e das direções usando nativo **mapeia** aplicativos no iOS e Android.

## <a name="devicestarttimer"></a>Device.StartTimer

O `Device` classe também tem um `StartTimer` método que fornece uma maneira simple para disparar tarefas dependentes de tempo que funciona em código comum do xamarin. Forms, incluindo uma biblioteca .NET Standard. Passar uma `TimeSpan` para definir o intervalo e retornar `true` para manter a execução do temporizador ou `false` para interrompê-la depois da chamada atual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se o código dentro do temporizador interage com a interface do usuário (como definir o texto de um `Label` ou exibindo um alerta) deve ser feita dentro de um `BeginInvokeOnMainThread` expressão (veja abaixo).

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir com a interface do usuário a partir de threads em segundo plano

A maioria dos sistemas operacionais, incluindo iOS, Android e o Plataforma Universal do Windows, usa um modelo de thread único para o código que envolve a interface do usuário. Esse thread geralmente é chamado de *thread principal* ou *thread de interface do usuário*. Uma consequência desse modelo é que todo o código que acessa elementos da interface do usuário deve ser executado no thread principal do aplicativo.

Os aplicativos às vezes usam threads em segundo plano para executar operações potencialmente demoradas, como recuperar dados de um serviço Web. Se o código em execução em um thread em segundo plano precisar acessar elementos da interface do usuário, ele deverá executar esse código no thread principal.

A `Device` classe inclui os seguintes `static` métodos que podem ser usados para interagir com elementos de interface do usuário de threads de planos de fundo:

| Método | Arguments | Retorna | Finalidade |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Invoca um `Action` no thread principal e não aguarda que ele seja concluído. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca um `Func<T>` no thread principal e aguarda a conclusão dele. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca um `Action` no thread principal e aguarda a conclusão dele. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca um `Func<Task<T>>` no thread principal e aguarda a conclusão dele. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca um `Func<Task>` no thread principal e aguarda a conclusão dele. |
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
- [Dispositivo](xref:Xamarin.Forms.Device)
