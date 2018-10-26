---
title: Operações de ponto flutuante no xamarin. IOS
description: Este documento descreve como o xamarin. IOS lida com operações de ponto flutuante de precisão 32 bits e 64 bits e discute os impactos associados ao desempenho.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: c5ee1b833e309c78c7338298cbe5c8800afb1ba1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116232"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operações de ponto flutuante no xamarin. IOS

Por padrão, o xamarin. IOS será executar 32 bits e 64 bits operações de ponto flutuante usando precisão de 64 bits em ARM.  

Enquanto esse maior precisão está perto o que os desenvolvedores esperam de operações de ponto flutuante C# na área de trabalho, em dispositivos móveis, o impacto no desempenho pode ser significativo.

É possível compilar o código de ponto flutuante 32 bits para usar operações de ponto flutuante de 32 bits.  Para fazer isso, você precisa usar pelo menos 8.10 do xamarin. IOS e o conjunto no iOS Criar painel do opções no "mtouch argumentos extras" o seguinte valor de linha de entrada:

     --aot-options=-O=float32

Isso informará os compiladores estáticos (compilador de estático interno do Mono, ou aquela energia LLVM) para executar operações de ponto flutuante usando flutuações de 32 bits.
