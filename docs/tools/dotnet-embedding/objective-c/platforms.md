---
title: Plataformas Objective-C
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 8c48a14e5d58f95bf93363e0b354fcf3afd94b44
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="objective-c-platforms"></a>Plataformas Objective-C

Inserindo .NET pode direcionar várias plataformas, ao gerar código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [ainda não implementada]

A plataforma está selecionada, passando o `--platform=<platform>` argumento de linha de comando para a incorporação de .NET.

Ao compilar para o iOS, plataformas tvOS e watchOS, inserindo .NET sempre criará uma estrutura que incorpora xamarin, pois xamarin contém muitos códigos de suporte em tempo de execução que é necessário nessas plataformas.

No entanto, ao compilar para a plataforma macOS, é possível escolher se o framework gerado deve incorporar Xamarin.Mac ou não. É possível inserir não Xamarin.Mac se o assembly associado não faz referência a Xamarin.Mac.dll (direta ou indiretamente), e isso é selecionado, passando `--platform=macOS` para a ferramenta de inserção de .NET.

Se o assembly associado contém uma referência a Xamarin.Mac.dll, é necessário inserir Xamarin.Mac e além do embeddinator deve saber qual estrutura de destino a ser usado.

Há três possíveis estruturas de destino Xamarin.Mac: `modern` (anteriormente chamado de `mobile`), `full` e `system` (a diferença entre cada um deles é descrita do Xamarin.Mac [framework de destino] [ 1] documentação), e cada um é selecionada, passando `--platform=macOS-modern`, `--platform=macOS-full` ou `--platform=macOS-system` para a ferramenta de inserção de .NET.

[1]: ~/mac/platform/target-framework.md
