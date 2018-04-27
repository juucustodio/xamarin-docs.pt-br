---
title: Classe de dispositivo
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 471616dffc700cf93a9f6435565222d7628bf165
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="device-class"></a>Classe de dispositivo

O [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe contém um número de propriedades e métodos para ajudar os desenvolvedores a personalizar o layout e a funcionalidade em uma base por plataforma.

Além dos métodos e propriedades para o código de destino em tipos específicos de hardware e tamanhos, o `Device` classe inclui o [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) método que deve ser usado ao interagir com a interface do usuário controla de threads em segundo plano.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Fornecendo valores específicos de plataforma

Antes de xamarin. Forms 2.3.4, a plataforma que o aplicativo estava em execução no pode ser obtida examinando o [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) propriedade e compará-lo para o [ `TargetPlatform.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/), [ `TargetPlatform.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/), [ `TargetPlatform.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), e [ `TargetPlatform.Windows` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) valores de enumeração. Da mesma forma, uma da [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) sobrecargas podem ser usadas para fornecer valores específicos de plataforma para um controle.

No entanto, desde o xamarin. Forms 2.3.4 essas APIs foram preteridas e substituídas. O [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe agora contém constantes de cadeia de caracteres pública que identificam plataformas – [ `Device.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), [ `Device.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), [ `Device.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/) (preterido) [ `Device.WinRT` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/) (preterido) [ `Device.UWP` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), e [ `Device.macOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/). Da mesma forma, o [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) sobrecargas foram substituídas com o [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) e [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) APIs.

Em c#, os valores específicos de plataforma podem ser fornecidos, criando um `switch` instrução no [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) propriedade e, em seguida, fornecendo `case` instruções para as plataformas necessárias:

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

O [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) e [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) classes fornecem a mesma funcionalidade em XAML:

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

O [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) classe é uma classe genérica e deve ser instanciado com um `x:TypeArguments` atributo que corresponde ao tipo de destino. No [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) classe, o [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) atributo pode aceitar um único `string` valor ou vários delimitada por vírgulas `string` valores.

> [!IMPORTANT]
> Fornecendo um incorreto `Platform` valor no atributo de `On` classe não resultará em um erro. Em vez disso, o código será executado sem o valor específico da plataforma que está sendo aplicado.

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

O `Device.Idiom` pode ser usado para alterar layouts ou funcionalidade dependendo do dispositivo, o aplicativo está em execução no. O [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/) enumeração contém os seguintes valores:

-  **Telefone** – iPhone, iPod touch e dispositivos com Android mais estreitos do que 600 dips ^
-  **Tablet** – iPad, dispositivos Windows e dispositivos com Android mais amplo que 600 dips ^
-  **Área de trabalho** – somente retornado em [aplicativos UWP](~/xamarin-forms/platform/windows/installation/index.md) em computadores desktop com Windows 10 (retorna `Phone` em dispositivos móveis do Windows, incluindo em cenários de continuidade)
-  **TV** – dispositivos Tizen TV
-  **Não há suporte para** – não utilizado

*^ dips não é necessariamente o número de pixels físicos*

`Idiom` é especialmente útil para a criação de layouts de aproveitar as telas maiores, como este:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

O [ `Styles` propriedade](~/xamarin-forms/user-interface/styles/index.md) contém definições de estilo internos que podem ser aplicadas a alguns controles (como `Label`) `Style` propriedade. Os estilos disponíveis são:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` pode ser usado ao definir [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) no código do c#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## <a name="deviceopenuri"></a>Device.OpenUri

O `OpenUri` método pode ser usado para disparar operações na plataforma subjacente, como abrir uma URL no navegador da web nativo (**Safari** no iOS ou **Internet** no Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

O [WebView exemplo](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) inclui um exemplo usando `OpenUri` para abrir URLs e também disparar chamadas telefônicas.

O [exemplo Maps](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) também usa `Device.OpenUri` para exibir mapas e direções usando nativo **mapeia** aplicativos em iOS e Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

O `Device` classe também tem um `StartTimer` método que fornece uma maneira simples para disparar tarefas dependentes de tempo que funciona no xamarin. Forms código comuns (incluindo PCLs). Passar um `TimeSpan` para definir o intervalo e retornar `true` para manter a execução do temporizador ou `false` para interrompê-lo após a chamada atual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se o código dentro do timer interage com a interface do usuário (como definir o texto de um `Label` ou exibir um alerta) deve ser feito em um `BeginInvokeOnMainThread` expressão (veja abaixo).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Elementos da interface do usuário nunca devem ser acessados por threads em segundo plano, como o código em execução em um timer ou um manipulador de conclusão para operações assíncronas, como solicitações da web. Qualquer código em segundo plano que precisa atualizar a interface do usuário deve ser encapsulado dentro de [ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/). Este é o equivalente de `InvokeOnMainThread` no iOS, `RunOnUiThread` no Android, e `Dispatcher.RunAsync` na plataforma Universal do Windows.

O código de xamarin. Forms é:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Observe que usando os métodos `async/await` não precisa usar `BeginInvokeOnMainThread` se estiverem executando o thread de interface do usuário principal.

## <a name="summary"></a>Resumo

O xamarin. Forms `Device` classe permite controle refinado layouts e funcionalidade em uma base por plataforma - código (PCL ou projetos compartilhados), mesmo em comum.


## <a name="related-links"></a>Links relacionados

- [Exemplo de dispositivo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Exemplo de estilos](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dispositivo](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
