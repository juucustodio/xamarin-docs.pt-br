---
title: Limitações de incorporação de .NET
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: a99d4f06b68e645ab1d0fc1423facb827b31959d
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="net-embedding-limitations"></a>Limitações de incorporação de .NET

Este documento explica as limitações de inserção de .NET e, sempre que possível, fornece soluções alternativas para eles.

## <a name="general"></a>Geral

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar mais de uma biblioteca inserida em um projeto

Não é possível ter dois tempos de execução Mono coexistindo dentro do mesmo aplicativo. Isso significa que você não pode usar duas bibliotecas .NET inserindo gerado diferentes dentro do mesmo aplicativo.

**Solução alternativa:** você pode usar o gerador de criar uma única biblioteca que inclui vários assemblies (de projetos diferentes).

### <a name="subclassing"></a>Subclasses

Inserindo .NET facilita a integração do tempo de execução Mono dentro de aplicativos ao expor um conjunto de APIs de prontos para uso para a plataforma e o idioma de destino.

No entanto, isso não é uma integração bidirecional, por exemplo, você não pode subclasse um tipo gerenciado e esperar que o código gerenciado a chamar dentro do código nativo, como o código gerenciado é não reconhece este coexistência.

Dependendo de suas necessidades, talvez seja possível a partes de solução alternativa dessa limitação, por exemplo

* o código gerenciado pode p/invoke em seu código nativo. Isso exige a personalizar o código gerenciado para permitir a personalização do código nativo;

* Use produtos como o xamarin e expor uma biblioteca gerenciada que permita que Objective-C (neste caso) a subclasse alguns gerenciados NSObject subclasses.

## <a name="objective-c-generated-code"></a>Código gerado Objective-C

### <a name="nullability"></a>Nulidade

Não há nenhum metadado no .NET que conte-nos se uma referência nula é aceitável ou não para uma API. A maioria das APIs lançarão `ArgumentNullException` se eles não podem lidar com um `null` argumento. Isso é problemático como Objective-C tratamento de exceções é uma opção melhor evitados.

Desde que não é possível gerar anotações de nulidade precisas nos arquivos de cabeçalho e deseja minimizar exceções gerenciadas é padrão para argumentos não nulos (`NS_ASSUME_NONNULL_BEGIN`) e adicione alguns específicos, quando a precisão é possível, as anotações de nulidade.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Atualmente a incorporação de .NET não dá suporte bitcode no iOS, que é habilitado para alguns modelos de projeto Xcode. Isso precisará ser desabilitado para estruturas de link gerado com êxito.

![Opção Bitcode](images/ios-bitcode-option.png)
