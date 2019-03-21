---
title: Extensões da plataforma do Xamarin.Essentials
description: O Xamarin.Essentials fornece vários métodos de extensão de plataforma quando é preciso trabalhar com tipos de plataforma, como retângulo, tamanho e o ponto.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 806fcb3fa90a5ec9d39cecb743b72116b8388a03
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58176008"
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

### <a name="point"></a>Ponto

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
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
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

## <a name="api"></a>API

- [Código-fonte de conversores](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentação da API de conversores de ponto](xref:Xamarin.Essentials.PointConverters)
- [Documentação da API de conversores de retângulo](xref:Xamarin.Essentials.RectangleConverters)
- [Documentação da API de conversores de tamanho](xref:Xamarin.Essentials.SizeConverters)
