---
title: Plataformas Objective-C
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 7dc4dc4ea0ab55ff603b9e8d6fa905336159222a
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="objective-c-platforms"></a>Plataformas Objective-C


Inserindo .NET pode direcionar várias plataformas, ao gerar código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [ainda não implementada]

A plataforma está selecionada, passando o `--platform=<platform>` argumento de linha de comando para o embeddinator.

Ao compilar para as plataformas do iOS, tvOS e watchOS, o embeddinator sempre criará uma estrutura que incorpora xamarin, pois xamarin contém muitos códigos de suporte em tempo de execução que é necessário nessas plataformas.

No entanto, ao compilar para a plataforma macOS, é possível escolher se o framework gerado deve incorporar Xamarin.Mac ou não. É possível inserir não Xamarin.Mac se o assembly associado não faz referência a Xamarin.Mac.dll (direta ou indiretamente), e isso é selecionado, passando `--platform=macOS` para o embeddinator.

Se o assembly associado contém uma referência a Xamarin.Mac.dll, é necessário inserir Xamarin.Mac e além do embeddinator deve saber qual estrutura de destino a ser usado.

Há três possíveis estruturas de destino Xamarin.Mac: `modern` (anteriormente chamado de `mobile`), `full` e `system` (a diferença entre cada um deles é descrita do Xamarin.Mac [framework de destino] [ 1] documentação), e cada um é selecionada, passando `--platform=macOS-modern`, `--platform=macOS-full` ou `--platform=macOS-system` para o embeddinator.

[1]: ~/mac/platform/target-framework.md
