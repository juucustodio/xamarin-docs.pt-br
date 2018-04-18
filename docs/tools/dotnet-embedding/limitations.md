---
title: Limitações de incorporação de .NET
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: d8995946966020955a1d9378dea631387ed5f4bd
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="net-embedding-limitations"></a>Limitações de incorporação de .NET

Este documento explica as limitações de inserção de .NET (Embeddinator-4000) e, sempre que possível, fornece soluções alternativas para eles.

## <a name="general"></a>Geral

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar mais de uma biblioteca inserida em um projeto

Não é possível ter dois tempos de execução mono coexistindo dentro do mesmo aplicativo. Isso significa que você não pode usar duas bibliotecas diferentes embeddinator 4000 gerado dentro do mesmo aplicativo.

**Solução alternativa:** você pode usar o gerador de criar uma única biblioteca que inclui vários assemblies (de projetos diferentes).

### <a name="subclassing"></a>Subclasses

O embeddinator facilita a integração do tempo de execução mono dentro de aplicativos ao expor um conjunto de APIs de prontos para uso para a plataforma e o idioma de destino.

No entanto, isso não é uma integração bidirecional, por exemplo, você não pode subclasse um tipo gerenciado e esperar que o código gerenciado a chamar dentro do código nativo, como o código gerenciado é não reconhece este coexistência.

Dependendo de suas necessidades, talvez seja possível a partes de solução alternativa dessa limitação, por exemplo

* o código gerenciado pode p/invoke em seu código nativo. Isso exige a personalizar o código gerenciado para permitir a personalização do código nativo;

* Use produtos como o xamarin e expor uma biblioteca gerenciada que permitisse ObjC (neste caso) subclasse alguns subclasses NSObject gerenciadas.


## <a name="objc-generated-code"></a>Código de ObjC gerado

### <a name="nullability"></a>Nulidade

Não há nenhum metadado no .NET, que conte-nos se uma referência nula é aceitável ou não para uma API. A maioria das APIs lançarão `ArgumentNullException` se eles não podem lidar com um `null` argumento. Isso é problemático como ObjC tratamento de exceções é uma opção melhor evitados.

Desde que não é possível gerar anotações de nulidade precisas nos arquivos de cabeçalho e deseja minimizar exceções gerenciadas é padrão para argumentos não nulos (`NS_ASSUME_NONNULL_BEGIN`) e adicione alguns específicos, quando a precisão é possível, as anotações de nulidade.
