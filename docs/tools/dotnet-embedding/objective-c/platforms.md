---
title: Plataformas Objective-C
description: Este documento descreve as várias plataformas que a incorporação do .NET pode direcionar ao trabalhar com o código Objective-C. Ele aborda o macOS, iOS, tvOS e watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: dbc2c40e06f14ec636b4aa4cc11fc4f2d230ee6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006419"
---
# <a name="objective-c-platforms"></a>Plataformas Objective-C

A incorporação .NET pode direcionar várias plataformas ao gerar o código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [ainda não implementado]

A plataforma é selecionada passando o argumento de linha de comando `--platform=<platform>` para inserção .NET.

Ao compilar para as plataformas iOS, tvOS e watchOS, a incorporação .NET sempre criará uma estrutura que incorpore Xamarin. iOS, já que o Xamarin. iOS contém muitos códigos de suporte de tempo de execução que são necessários nessas plataformas.

No entanto, ao criar para a plataforma macOS, é possível escolher se a estrutura gerada deve inserir Xamarin. Mac ou não. É possível não inserir o Xamarin. Mac se o assembly associado não referenciar o Xamarin. Mac. dll (direta ou indiretamente) e isso é selecionado passando `--platform=macOS` para a ferramenta de inserção .NET.

Se o assembly associado contiver uma referência a Xamarin. Mac. dll, será necessário inserir o Xamarin. Mac e, além disso, o embeddinator deverá saber qual estrutura de destino usar.

Há três estruturas de destino Xamarin. Mac possíveis: `modern` (anteriormente chamado de `mobile`), `full` e `system` (a diferença entre cada uma é descrita na documentação da [estrutura de destino][1] do Xamarin. Mac) e cada uma é selecionada passando `--platform=macOS-modern`, `--platform=macOS-full` ou `--platform=macOS-system` à ferramenta de inserção .NET.

[1]: ~/mac/platform/target-framework.md
