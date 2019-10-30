---
title: Operações de ponto flutuante no Xamarin. iOS
description: Este documento descreve como o Xamarin. iOS trata as operações de ponto flutuante de precisão de 32 bits e 64 bits e discute os impactos associados ao desempenho.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: af0e37b41a7bbe831bc629b1fd916f62819b3711
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022334"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operações de ponto flutuante no Xamarin. iOS

O Xamarin. iOS, por padrão, executa operações de ponto flutuante de 32 bits e 64 bits usando a precisão de 64 bits no ARM.  

Embora essa maior precisão esteja mais próxima do que os desenvolvedores esperam de operações de C# ponto flutuante no desktop, em dispositivos móveis, o impacto no desempenho pode ser significativo.

É possível compilar seu código de ponto flutuante de 32 bits para usar operações de ponto flutuante de 32 bits.  Para fazer isso, você precisa usar pelo menos o Xamarin. iOS 8,10 e definir no painel de opções de Build do iOS na linha de entrada "mTouch extras Arguments", o seguinte valor:

```
--aot-options=-O=float32
```

Isso informará os compiladores estáticos (compilador estático interno do mono ou o LLVM) para executar operações de ponto flutuante usando floats de 32 bits.
