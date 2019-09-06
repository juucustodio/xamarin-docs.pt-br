---
title: Integrações de amostra
description: Este documento contém links para exemplos que demonstram integrações de Xamarin Workbooks. Exemplos vinculados funcionam com renderização de representação e SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: fbe471aa7f08d85a870d68505cf2c983b7e442e9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292807"
---
# <a name="sample-integrations"></a>Integrações de amostra

Consulte o exemplo de [coletor de cozinha][KitchenSink] para obter um exemplo de trabalho de uma integração. Basta compilar `KitchenSink.sln` no Visual Studio para Mac ou no Visual Studio e, `KitchenSink.workbook`em seguida, abrir.

[![Captura de tela de integração do coletor de cozinha](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

O exemplo de coletor de cozinha demonstra os dois conjuntos de conceitos:

* As partes de representação demonstram como `RepresentationManager` usar o para aprimorar a renderização usando as representações internas.
* O `Person` objeto e seu processador JavaScript associado demonstram `ISerializableObject` o uso sem passar por um provedor de representação.

Consulte também [SkiaSharp][skiasharp] para obter um exemplo do mundo real de uma integração que usa as [representações](~/tools/workbooks/sdk/representations.md) existentes fornecidas pelo Xamarin Workbooks para processar seus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
