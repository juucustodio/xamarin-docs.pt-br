---
title: Integrações de amostra
description: Este documento contém links para exemplos que demonstram integrações de Xamarin Workbooks. Exemplos vinculados funcionam com renderização de representação e SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: fa66ee2b2b469900381e2d31dc5dec49eb42c4f6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018118"
---
# <a name="sample-integrations"></a>Integrações de amostra

Consulte o exemplo de [coletor de cozinha][KitchenSink] para obter um exemplo de trabalho de uma integração. Basta criar `KitchenSink.sln` no Visual Studio para Mac ou no Visual Studio e, em seguida, abrir `KitchenSink.workbook`.

[Captura de tela de integração do coletor de cozinha![](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

O exemplo de coletor de cozinha demonstra os dois conjuntos de conceitos:

* As partes de representação demonstram como usar `RepresentationManager` para aprimorar a renderização usando as representações internas.
* O objeto `Person` e seu renderizador JavaScript associado demonstram o uso de `ISerializableObject` sem passar por um provedor de representação.

Consulte também [SkiaSharp][skiasharp] para obter um exemplo do mundo real de uma integração que usa as [representações](~/tools/workbooks/sdk/representations.md) existentes fornecidas pelo Xamarin Workbooks para processar seus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
