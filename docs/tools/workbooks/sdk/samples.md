---
title: Integrações de exemplo
description: Este documento leva a exemplos que demonstram as integrações de pastas de trabalho do Xamarin. Exemplos de vinculado funcionam com SkiaSharp e renderização de representação.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117415"
---
# <a name="sample-integrations"></a>Integrações de exemplo

Consulte a [cozinha Sink] [ KitchenSink] exemplo para um exemplo de uma integração. Compilar simplesmente `KitchenSink.sln` no Visual Studio para Mac ou Visual Studio e abra `KitchenSink.workbook`.

[![Captura de tela de integração de coletor cozinha](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

O exemplo de coletor cozinha demonstra os dois conjuntos de conceitos:

* As partes de representação demonstram como usar `RepresentationManager` para melhorar a renderização usando as representações internas.
* O `Person` objeto e seu renderizador JavaScript associado demonstram o uso de `ISerializableObject` sem passar por um provedor de representação.

Consulte também [SkiaSharp] [ skiasharp] para obter um exemplo de uma integração que usa existente reais [representações](~/tools/workbooks/sdk/representations.md) fornecidos pelo Xamarin Workbooks para processar seus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
