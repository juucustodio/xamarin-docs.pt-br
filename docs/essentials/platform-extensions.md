---
title: Extensões da plataforma do Xamarin.Essentials
description: O Xamarin.Essentials fornece vários métodos de extensão de plataforma quando é preciso trabalhar com tipos de plataforma, como retângulo, tamanho e o ponto.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
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

## <a name="android-extensions"></a>Extensões androides

Essas extensões só podem ser acessadas a partir de um projeto Android.

### <a name="application-context--activity"></a>Contexto de aplicação & atividade

Usando as extensões `Platform` da plataforma na classe, `Context` `Activity` você pode ter acesso ao aplicativo atual ou para o aplicativo em execução.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

Se houver uma situação em que o `Activity` aplicativo seja necessário, mas a aplicação ainda não tenha sido totalmente iniciada, o `WaitForActivityAsync` método deve ser usado.

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>Ciclo de vida de atividade

Além de obter a atividade atual, você também pode se registrar para eventos do ciclo de vida.

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

Os estados de atividade são os seguintes:

* Criado
* Retomada
* Em Pausa
* Destruído
* SaveInstanceState
* Started (iniciado)
* Parado

Leia a documentação do [ciclo de vida da atividade](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) para saber mais.

## <a name="ios-extensions"></a>Extensões do iOS

Essas extensões só podem ser acessadas a partir de um projeto iOS.

### <a name="current-uiviewcontroller"></a>UIViewcontroller atual

Obtenha acesso ao atualmente visível: `UIViewController`

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Este método `null` retornará se `UIViewController`não conseguir detectar um .

## <a name="cross-platform-extensions"></a>Extensões multiplataforma

Essas extensões existem em todas as plataformas.

### <a name="point"></a>Point

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
