---
title: Plataformas Objective-C
description: Este documento descreve as várias plataformas .NET inserindo pode destinar ao trabalhar com código Objective-C. Ele discute macOS, iOS, tvOS e watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 6eeb776959d1a2a37d67bfae6603971d0e5a22b7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793885"
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
