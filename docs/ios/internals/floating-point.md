---
title: Operações de ponto flutuante em xamarin
description: Este documento descreve como xamarin lida com operações de ponto flutuante de precisão de 32 bits e 64 bits e discute associados impactos no desempenho.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ea5d69b52cbd4c76abb236bd1a272633dde440b7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786155"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operações de ponto flutuante em xamarin

Por padrão, o xamarin será executar 32 bits e 64 bits operações de ponto flutuante com precisão de 64 bits em ARM.  

Enquanto esse maior precisão está mais próximo que os desenvolvedores de espera de operações de ponto flutuante em c# na área de trabalho, no dispositivo móvel, que o impacto no desempenho pode ser significativo.

É possível compilar o código ponto flutuante de 32 bits para usar as operações de ponto flutuante de 32 bits.  Para fazer isso, você precisa usar pelo menos 8.10 xamarin e um conjunto na sua iOS basear painel do opções de "mtouch argumentos extras" o seguinte valor de linha de entrada:

     --aot-options=-O=float32

Isso informará os compiladores estáticos (compilador de estático interno da Mono, ou a uma potência LLVM) para executar operações de ponto flutuante usando floats de 32 bits.
