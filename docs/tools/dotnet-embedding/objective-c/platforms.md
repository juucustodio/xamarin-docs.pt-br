---
title: Plataformas Objective-C
description: Este documento descreve as várias plataformas que a incorporação do .NET pode direcionar ao trabalhar com o código Objective-C. Ele aborda o macOS, iOS, tvOS e watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: f97b595f129cb1ad1ea56e3ae43b0f0a477fef5a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282726"
---
# <a name="objective-c-platforms"></a>Plataformas Objective-C

A incorporação .NET pode direcionar várias plataformas ao gerar o código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [ainda não implementado]

A plataforma é selecionada passando o `--platform=<platform>` argumento de linha de comando para inserção .net.

Ao compilar para as plataformas iOS, tvOS e watchOS, a incorporação .NET sempre criará uma estrutura que incorpore Xamarin. iOS, já que o Xamarin. iOS contém muitos códigos de suporte de tempo de execução que são necessários nessas plataformas.

No entanto, ao criar para a plataforma macOS, é possível escolher se a estrutura gerada deve inserir Xamarin. Mac ou não. É possível não inserir o xamarin. Mac se o assembly associado não referenciar xamarin. Mac. dll (direta ou indiretamente), e isso é selecionado passando `--platform=macOS` para a ferramenta de inserção do .net.

Se o assembly associado contiver uma referência a Xamarin. Mac. dll, será necessário inserir o Xamarin. Mac e, além disso, o embeddinator deverá saber qual estrutura de destino usar.

Há três estruturas de destino Xamarin. `modern` Mac possíveis: (anteriormente chamado `mobile`) `full` e `system` (a diferença entre cada uma é descrita na documentação da [estrutura de destino][1] do Xamarin. Mac) e cada uma delas é selecionado passando `--platform=macOS-modern`, `--platform=macOS-full` ou `--platform=macOS-system` para a ferramenta de inserção .net.

[1]: ~/mac/platform/target-framework.md
