---
title: "Integrações de exemplo"
ms.topic: article
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: f71da0f522c6c028981637a9797c3836063516f0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="sample-integrations"></a>Integrações de exemplo

## <a name="sample-integrations"></a>Integrações de exemplo

Consulte o [cozinha coletor] [ KitchenSink] de exemplo para um exemplo de uma integração. Basta criar `KitchenSink.sln` no Visual Studio para Mac ou o Visual Studio e abra `KitchenSink.workbook`.

[![Captura de tela de integração de coletor cozinha](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

O exemplo da cozinha coletor demonstra os dois conjuntos de conceitos:

* As partes de representação demonstram como usar `RepresentationManager` para aumentar a renderização usando as representações internas.
* O `Person` objeto e seu associado renderizador de JavaScript demonstram o uso `ISerializableObject` sem passar por um provedor de representação.

Consulte também [SkiaSharp] [ skiasharp] para obter um exemplo de uma integração que usa existente reais [representações](~/tools/workbooks/sdk/representations.md) fornecida pelo Xamarin pastas de trabalho para processar seus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks