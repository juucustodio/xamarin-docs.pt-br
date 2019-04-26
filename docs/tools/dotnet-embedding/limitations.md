---
title: Limitações de incorporação do .NET
description: Este documento descreve as limitações de incorporação do .NET, a ferramenta que permite que você consuma o código .NET em outras linguagens de programação.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 7a162d632c98b4e412fa1b7b0c0c40ac945ff09f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945773"
---
# <a name="net-embedding-limitations"></a>Limitações de incorporação do .NET

Este documento explica as limitações de incorporação do .NET e, sempre que possível, fornece soluções alternativas para eles.

## <a name="general"></a>Geral

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar mais de uma biblioteca incorporada em um projeto

Não é possível ter dois tempos de execução Mono coexistam no mesmo aplicativo. Isso significa que você não pode usar duas bibliotecas diferentes incorporação do .NET gerados dentro do mesmo aplicativo.

**Solução alternativa:** Você pode usar o gerador para criar uma única biblioteca que inclui vários assemblies (de projetos diferentes).

### <a name="subclassing"></a>Criando subclasses

Incorporação do .NET facilita a integração do tempo de execução Mono dentro de aplicativos, expondo um conjunto de APIs prontos para uso para o idioma de destino e plataforma.

No entanto, isso não é uma integração bidirecional, por exemplo, você não pode subclasse um tipo gerenciado e esperar que o código gerenciado chame dentro do código nativo, desde que seu código gerenciado desconhece esse coexistência.

Dependendo das suas necessidades, pode ser possível às partes da solução alternativa essa limitação, por exemplo

* o código gerenciado pode p/invoke em seu código nativo. Isso exige a personalização de seu código gerenciado para permitir a personalização do código nativo;

* Use produtos como o xamarin. IOS e expor uma biblioteca gerenciada que permitiria a que Objective-C (neste caso) a subclasse alguns gerenciados NSObject subclasses.

## <a name="objective-c-generated-code"></a>Código gerado do Objective-C

### <a name="nullability"></a>nulidade

Não há nenhum metadado no .NET que conte-nos se uma referência nula é aceitável ou não para uma API. A maioria das APIs lançarão `ArgumentNullException` se eles não podem lidar com um `null` argumento. Isso é problemático, como Objective-C de tratamento de exceções é algo melhor evitado.

Uma vez que estamos não é possível gerar anotações de nulidade precisas nos arquivos de cabeçalho e deseja minimizar exceções gerenciadas o padrão para argumentos não nulos (`NS_ASSUME_NONNULL_BEGIN`) e adicionar alguns específicos, quando a precisão for possível, as anotações de nulidade.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Atualmente, a incorporação de .NET não oferece suporte bitcode no iOS, que é habilitado para alguns modelos de projeto do Xcode. Isso precisará ser desabilitado para estruturas de link gerado com êxito.

![Opção de Bitcode](images/ios-bitcode-option.png)
