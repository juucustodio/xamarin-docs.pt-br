---
title: Plataformas de Objective-C
description: Este documento descreve as várias plataformas .NET incorporação pode direcionar ao trabalhar com código Objective-C. Ele aborda o macOS, iOS, tvOS e watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8091fb4e8328f61f1471d061b51b4735de3c089c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108868"
---
# <a name="objective-c-platforms"></a>Plataformas de Objective-C

Incorporação do .NET pode direcionar várias plataformas, ao gerar o código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [ainda não implementado]

A plataforma é selecionada, passando o `--platform=<platform>` argumento de linha de comando para incorporação do .NET.

Ao compilar para iOS, tvOS e watchOS plataformas, incorporação do .NET sempre criará uma estrutura que incorpora o xamarin. IOS, como xamarin. IOS contém muitos códigos de suporte de tempo de execução que é necessário nessas plataformas.

No entanto, ao compilar para a plataforma macOS, é possível escolher se o framework gerado deve incorporar o xamarin. Mac ou não. É possível inserir não o xamarin. Mac se o assembly associado não faz referência xamarin (direta ou indiretamente), e essa opção é selecionada, passando `--platform=macOS` para a ferramenta de incorporação do .NET.

Se o assembly associado contiver uma referência ao xamarin, é necessário incorporar o xamarin. Mac e, adicionalmente o embeddinator precisa saber qual estrutura de destino para usar.

Há três possíveis estruturas de destino de xamarin. Mac: `modern` (anteriormente chamado `mobile`), `full` e `system` (a diferença entre cada um é descrita do xamarin. Mac [estrutura de destino] [ 1] documentação), e cada um é selecionada, passando `--platform=macOS-modern`, `--platform=macOS-full` ou `--platform=macOS-system` para a ferramenta de incorporação do .NET.

[1]: ~/mac/platform/target-framework.md
