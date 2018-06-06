---
title: Integrações de exemplo
description: Links neste documento para exemplos que demonstram integrações de pastas de trabalho do Xamarin. Exemplos vinculados trabalham com SkiaSharp e a renderização de representação.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 75d88af4c294a35d45f05724eb96ce822cddf126
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793965"
---
# <a name="sample-integrations"></a>Integrações de exemplo

Consulte o [cozinha coletor] [ KitchenSink] de exemplo para um exemplo de uma integração. Basta criar `KitchenSink.sln` no Visual Studio para Mac ou o Visual Studio e abra `KitchenSink.workbook`.

[![Captura de tela de integração de coletor cozinha](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

O exemplo da cozinha coletor demonstra os dois conjuntos de conceitos:

* As partes de representação demonstram como usar `RepresentationManager` para aumentar a renderização usando as representações internas.
* O `Person` objeto e seu associado renderizador de JavaScript demonstram o uso `ISerializableObject` sem passar por um provedor de representação.

Consulte também [SkiaSharp] [ skiasharp] para obter um exemplo de uma integração que usa existente reais [representações](~/tools/workbooks/sdk/representations.md) fornecida pelo Xamarin pastas de trabalho para processar seus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
