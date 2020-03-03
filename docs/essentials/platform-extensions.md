---
title: Extensões da plataforma do Xamarin.Essentials
description: O Xamarin.Essentials fornece vários métodos de extensão de plataforma quando é preciso trabalhar com tipos de plataforma, como retângulo, tamanho e o ponto.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545155"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Extensões de plataforma

O Xamarin.Essentials fornece vários métodos de extensão de plataforma quando é preciso trabalhar com tipos de plataforma, como retângulo, tamanho e o ponto. Isso significa que você pode converter entre a versão `System` desses tipos para iOS, Android e tipos específicos de UWP. 

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Usando as extensões de plataforma

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Todas as extensões de plataforma só podem ser chamadas do iOS, do Android ou do projeto UWP.

## <a name="android-extensions"></a>Extensões do Android

Essas extensões só podem ser acessadas por um projeto Android.

### <a name="application-context--activity"></a>Atividade e contexto do aplicativo

Usando as extensões de plataforma na classe `Platform`, você pode acessar o `Context` ou o `Activity` atuais do aplicativo em execução.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

Se houver uma situação em que o `Activity` seja necessário, mas o aplicativo ainda não tenha sido totalmente iniciado, o método `WaitForActivityAsync` deverá ser usado.

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>Ciclo de vida de atividade

Além de obter a Atividade atual, você também pode registrar os eventos do ciclo de vida.

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

Os estados da atividade são os seguintes:

* Criado
* Retomada
* Paused
* Destruído
* SaveInstanceState
* Introdução
* Parado

Leia a documentação do [Ciclo de vida da atividade](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) para saber mais.

## <a name="ios-extensions"></a>Extensões do iOS

Essas extensões só podem ser acessadas por um projeto iOS.

### <a name="current-uiviewcontroller"></a>UIViewController atual

Obtenha acesso ao `UIViewController` atualmente visível:

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Esse método retornará `null` se não for possível detectar um `UIViewController`.

## <a name="cross-platform-extensions"></a>Extensões multiplataforma

Essas extensões existem em todas as plataformas.

### <a name="point"></a>Ponto

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>Tamanho

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>Retângulo

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [Código-fonte de conversores](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentação da API de conversores de ponto](xref:Xamarin.Essentials.PointExtensions)
- [Documentação da API de conversores de retângulo](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentação da API de conversores de tamanho](xref:Xamarin.Essentials.SizeExtensions)
