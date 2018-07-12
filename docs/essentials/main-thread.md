---
title: 'Xamarin.Essentials: MainThread'
description: A classe MainThread permite que os aplicativos executar o código no thread principal de execução.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831419"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **MainThread** classe permite que os aplicativos executar o código no thread principal de execução e determinar se um determinado bloco de código está sendo executado no thread principal.

## <a name="background"></a>Informações preliminares

A maioria dos sistemas operacionais — incluindo iOS, Android e plataforma Universal do Windows — use um modelo de threading simples para o código que envolvem a interface do usuário. Esse modelo é necessário para serializar os eventos de interface do usuário, incluindo pressionamentos de tecla e entrada por toque corretamente. Este thread é frequentemente chamado de _thread principal_ ou o _thread de interface do usuário_ ou o _thread de interface do usuário_. A desvantagem desse modelo é que todo o código que acessa os elementos da interface do usuário deve ser executado no thread principal do aplicativo. 

Às vezes, os aplicativos precisam usar os eventos que chama o manipulador de eventos em um thread secundário de execução. (As classes de Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), e [ `OrientationSensor` ](orientation-sensor.md) todos podem retornar informações em um thread secundário, quando usado com velocidades mais rápidas.) Se o manipulador de eventos precisa acessar os elementos de interface do usuário, ele deve executar esse código no thread principal. O **MainThread** classe permite que o aplicativo executar esse código no thread principal.

## <a name="running-code-on-the-main-thread"></a>Código em execução no Thread principal

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para executar o código no thread principal, chame estático `MainThread.BeginInvokeOnMainThread` método. O argumento é uma [ `Action` ](xref:System.Action) objeto, que é simplesmente um método com nenhum argumento e nenhum valor de retorno:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
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
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin. Forms tem um método chamado [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) que faz a mesma coisa que `MainThread.BeginInvokeOnMainThread(Action)`. Embora você possa usar qualquer um dos métodos em um aplicativo xamarin. Forms, considere se o código de chamada tem qualquer outra necessidade de uma dependência no xamarin. Forms. Caso contrário, `MainThread.BeginInvokeOnMainThread(Action)` provavelmente é uma opção melhor.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Determinando se o código está em execução no Thread da principal

O `MainThread` classe também permite que um aplicativo para determinar se um determinado bloco de código está em execução no thread principal. O `IsMainThread` propriedade retorna `true` se o código chamar a propriedade estiver em execução no thread principal. Um programa pode usar essa propriedade para executar um código diferente para o thread principal ou em um thread secundário:

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Você talvez esteja se perguntando se você deve verificar se o código está em execução em um thread secundário antes de chamar `BeginInvokeOnMainThread`, por exemplo, como este:

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Você pode suspeitar que essa verificação pode melhorar o desempenho se o bloco de código já está em execução no thread principal.

_No entanto, essa verificação não é necessária._ As implementações de plataforma de `BeginInvokeOnMainThread` próprios verificar se a chamada é feita no thread principal. Não há penalidade de desempenho muito pouco se você chamar `BeginInvokeOnMainThread` quando não é realmente necessário.

## <a name="api"></a>API

- [Código-fonte MainThread](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Documentação da API MainThread](xref:Xamarin.Essentials.MainThread)
