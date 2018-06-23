---
title: Plataforma Xamarin.Essentials
description: A classe de plataforma permite que aplicativos executar o código no thread de execução principal.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E0
author: charlespetzold
ms.author: chape
ms.date: 06/03/2018
ms.openlocfilehash: 82e248ee702e104dff98b342aec72179273fc34f
ms.sourcegitcommit: d9ecac62bcf743aff52408fbbd09c5509921a000
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36329943"
---
# <a name="xamarinessentials-platform"></a>Plataforma Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **plataforma** classe permite que os aplicativos executar o código no thread principal de execução e determinar se um determinado bloco de código está sendo executado no thread principal.

## <a name="background"></a>Informações preliminares

A maioria dos sistemas operacionais, incluindo iOS, Android e a plataforma Universal do Windows — use um modelo de thread único para o código que envolvem a interface do usuário. Esse modelo é necessário para serializar eventos da interface do usuário, incluindo pressionamentos de tecla e entrada por toque corretamente. Esse thread é geralmente chamado de _thread principal_ ou _thread de interface do usuário_ ou o _thread de interface do usuário_. A desvantagem desse modelo é que todo o código que acessa os elementos da interface do usuário deve ser executada no thread principal do aplicativo. 

Às vezes, os aplicativos precisam usar eventos que chamar o manipulador de eventos em um thread secundário de execução. (As classes de Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), e [ `OrientationSensor` ](orientation-sensor.md) todos podem retornar informações em um thread secundário quando usado com mais rapidez.) Se o manipulador de eventos precisa acessar elementos de interface do usuário, esse código deve ser executada no thread principal. O **plataforma** classe permite que o aplicativo executar esse código no thread principal.

## <a name="running-code-on-the-main-thread"></a>Código em execução no Thread principal

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para executar o código no thread principal, chame estático `Platform.BeginInvokeOnMainThread` método. O argumento é uma [ `Action` ](xref:System.Action) objeto, que é simplesmente um método com nenhum argumento e nenhum valor de retorno:

```csharp
Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Se você precisar chamar este método em um aplicativo xamarin. Forms dentro de uma classe que deriva de `Element` (que inclui qualquer classe que deriva de `View` ou `Page`), o `Platform` nome da classe deve ser totalmente qualificado com o nome do namespace:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Também é possível definir um método separado para o código que deve ser executado no thread principal:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

Você pode executar esse método no thread principal, fazendo referência a ele no `BeginInvokeOnMainThread` método:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin. Forms tem um método chamado [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) que faz a mesma coisa que `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)`. Embora você possa usar qualquer um dos métodos em um aplicativo xamarin. Forms, considere se o código de chamada tem necessidade de uma dependência no xamarin. Forms. Caso contrário, `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)` provavelmente é uma opção melhor.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinar se o código está em execução no Thread da principal

O `Platform` classe também permite que um aplicativo determinar se um determinado bloco de código está em execução no thread principal. O `IsMainThread` propriedade retorna `true` se o código de chamada a propriedade estiver em execução no thread principal. Um programa pode usar essa propriedade para executar código diferente para o thread principal ou em um thread secundário:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Você talvez esteja se perguntando se você deve verificar se o código está sendo executado em um thread secundário antes de chamar `BeginInvokeOnMainThread`, por exemplo, como este:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Você pode suspeitar que essa verificação pode melhorar o desempenho se o bloco de código já está em execução no thread principal.

_No entanto, essa verificação não é necessária._ As implementações de plataforma de `BeginInvokeOnMainThread` se verificar se a chamada é feita no thread principal. Há muito pouco penalidade de desempenho se você chamar `BeginInvokeOnMainThread` quando não é realmente necessário.

## <a name="api"></a>API

- [Código de plataforma](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Platform)
- [Documentação da API de plataforma](xref:Xamarin.Essentials.Platform)
