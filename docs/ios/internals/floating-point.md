---
title: Operações de ponto flutuante no Xamarin. iOS
description: Este documento descreve como o Xamarin. iOS trata as operações de ponto flutuante de precisão de 32 bits e 64 bits e discute os impactos associados ao desempenho.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 1ecb00fecaf14afb8c6d5c59297eb26821ed791a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291921"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operações de ponto flutuante no Xamarin. iOS

O Xamarin. iOS, por padrão, executa operações de ponto flutuante de 32 bits e 64 bits usando a precisão de 64 bits no ARM.  

Embora essa maior precisão esteja mais próxima do que os desenvolvedores esperam de operações de C# ponto flutuante no desktop, em dispositivos móveis, o impacto no desempenho pode ser significativo.

É possível compilar seu código de ponto flutuante de 32 bits para usar operações de ponto flutuante de 32 bits.  Para fazer isso, você precisa usar pelo menos o Xamarin. iOS 8,10 e definir no painel de opções de Build do iOS na linha de entrada "mTouch extras Arguments", o seguinte valor:

```
--aot-options=-O=float32
```

Isso informará os compiladores estáticos (compilador estático interno do mono ou o LLVM) para executar operações de ponto flutuante usando floats de 32 bits.
