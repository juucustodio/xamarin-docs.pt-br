---
title: 'Xamarin.Essentials: MainThread'
description: A classe MainThread permite que os aplicativos executem o código no thread de execução principal.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 08/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0dc8981024105e24ef4dcd725284723060e7313c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432319"
---
# <a name="no-locxamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

A classe **MainThread** permite que os aplicativos executem o código no thread de execução principal e determinem se certo bloco de código está sendo executado no thread principal.

## <a name="background"></a>Segundo plano

A maioria dos sistemas operacionais, incluindo iOS, Android e a Plataforma Universal do Windows, usa um modelo de threading simples para o código que envolve a interface do usuário. Esse modelo é necessário para serializar adequadamente os eventos da interface do usuário, incluindo pressionamentos de tecla e entrada de toque. Este thread é frequentemente chamado de _thread principal_ ou o _thread da interface do usuário_ ou, ainda, o _thread da IU_. A desvantagem desse modelo é que todo código que acessa os elementos da interface do usuário deve ser executado no thread principal do aplicativo.

Às vezes, os aplicativos precisam usar eventos que chamam o manipulador de eventos em um thread de execução secundário. (As Xamarin.Essentials classes [`Accelerometer`](accelerometer.md) , [`Compass`](compass.md) , [`Gyroscope`](gyroscope.md) , [`Magnetometer`](magnetometer.md) e [`OrientationSensor`](orientation-sensor.md) All podem retornar informações sobre um thread secundário quando usadas com velocidades mais rápidas.) Se o manipulador de eventos precisar acessar elementos da interface do usuário, ele deverá executar esse código no thread principal. A classe **MainThread** permite que os aplicativos executem esse código no thread principal.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="running-code-on-the-main-thread"></a>Como executar códigos no Thread Principal

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para executar o código no thread principal, chame o método `MainThread.BeginInvokeOnMainThread` estático. O argumento é um [`Action`](xref:System.Action) objeto, que é simplesmente um método sem argumentos e nenhum valor de retorno:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Também é possível definir um método separado para o código que deverá ser executado no thread principal:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

Você pode executar esse método no thread principal fazendo referência a ele no método `BeginInvokeOnMainThread`:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms tem um método chamado [`Device.BeginInvokeOnMainThread(Action)`](/dotnet/api/xamarin.forms.device.begininvokeonmainthread)
> que faz o mesmo que `MainThread.BeginInvokeOnMainThread(Action)`.
> Embora você possa usar qualquer um dos métodos em um Xamarin.Forms aplicativo, considere se o código de chamada tem alguma necessidade de uma dependência Xamarin.Forms . Caso contrário, é provável que `MainThread.BeginInvokeOnMainThread(Action)` seja uma opção melhor.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Como determinar se o código está em execução no thread principal

A classe `MainThread` também permite que um aplicativo determine se certo bloco de código está em execução no thread principal. A propriedade `IsMainThread` retorna `true` se o código que chama a propriedade estiver em execução no thread principal. Um programa pode usar essa propriedade para executar um código diferente no thread principal ou em um thread secundário:

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

Você pode se perguntar se deve verificar se o código está sendo executado em um thread secundário antes de chamar `BeginInvokeOnMainThread`, por exemplo:

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

Você pode suspeitar que essa verificação possa melhorar o desempenho se o bloco de código já estiver em execução no thread principal.

_No entanto, essa verificação não é necessária._ As implementações da plataforma de `BeginInvokeOnMainThread` verificam se a chamada é feita no thread principal. A perda de desempenho é pouca se você chamar `BeginInvokeOnMainThread` quando não for realmente necessário.

## <a name="additional-methods"></a>Métodos adicionais

A classe `MainThread` inclui os seguintes métodos `static` adicionais que podem ser usados para interagir com elementos da interface do usuário dos threads em segundo plano:

| Método | Argumentos | Retornos | Finalidade |
|---|---|---|---|
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca um `Func<T>` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca um `Action` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca um `Func<Task<T>>` no thread principal e aguarda sua conclusão. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca um `Func<Task>` no thread principal e aguarda sua conclusão. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retorna o `SynchronizationContext` para o thread principal. |

## <a name="api"></a>API

- [Código-fonte do MainThread](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MainThread)
- [Documentação da API do MainThread](xref:Xamarin.Essentials.MainThread)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Main-Thread-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]