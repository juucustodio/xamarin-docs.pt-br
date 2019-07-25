---
title: Integrações de amostra
description: Este documento leva a exemplos que demonstram as integrações de pastas de trabalho do Xamarin. Exemplos de vinculado funcionam com SkiaSharp e renderização de representação.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424590"
---
# <a name="sample-integrations"></a>Integrações de amostra

Consulte a [cozinha Sink] [ KitchenSink] exemplo para um exemplo de uma integração. Compilar simplesmente `KitchenSink.sln` no Visual Studio para Mac ou Visual Studio e abra `KitchenSink.workbook`.

[![Captura de tela de integração de coletor cozinha](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

O exemplo de coletor cozinha demonstra os dois conjuntos de conceitos:

* As partes de representação demonstram como usar `RepresentationManager` para melhorar a renderização usando as representações internas.
* O `Person` objeto e seu renderizador JavaScript associado demonstram o uso de `ISerializableObject` sem passar por um provedor de representação.

Consulte também [SkiaSharp][skiasharp] para obter um exemplo de uma integração que usa existente reais [representações](~/tools/workbooks/sdk/representations.md) fornecidos pelo Xamarin Workbooks para processar seus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
