---
title: Limitações de incorporação do .NET
description: Este documento descreve as limitações da inserção do .NET, a ferramenta que permite que você consuma o código .NET em outras linguagens de programação.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: a8b63638861e8d44deb4ea72959d7461190f7713
ms.sourcegitcommit: 6266ef043ae0289f174e901f204f2a280a53c071
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545800"
---
# <a name="net-embedding-limitations"></a>Limitações de incorporação do .NET

Este documento explica as limitações da incorporação do .NET e, sempre que possível, fornece soluções alternativas para elas.

## <a name="general"></a>Geral

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar mais de uma biblioteca incorporada em um projeto

Não é possível ter dois tempos de execução do mono coexistentes dentro do mesmo aplicativo. Isso significa que você não pode usar duas bibliotecas diferentes geradas na inserção do .NET dentro do mesmo aplicativo.

**Solução alternativa:** Você pode usar o gerador para criar uma única biblioteca que inclui vários assemblies (de projetos diferentes).

### <a name="subclassing"></a>Subclasses

A incorporação do .NET facilita a integração do tempo de execução mono dentro de aplicativos expondo um conjunto de APIs prontas para uso para a plataforma e o idioma de destino.

No entanto, essa não é uma integração bidirecional, por exemplo, você não pode fazer uma subclasse de um tipo gerenciado e esperar que o código gerenciado chame de volta dentro de seu código nativo, já que seu código gerenciado não está ciente dessa coexistência.

Dependendo de suas necessidades, pode ser possível fazer parte da solução alternativa dessa limitação, por exemplo,

* seu código gerenciado pode p/Invoke em seu código nativo. Isso exige a personalização do código gerenciado para permitir a personalização do código nativo;

* use produtos como o Xamarin. iOS e expor uma biblioteca gerenciada que permitiria o Objective-C (nesse caso) para a subclasse de algumas subclasses NSObject gerenciadas.

## <a name="objective-c-generated-code"></a>Código gerado pelo Objective-C

### <a name="nullability"></a>Nulidade

Não há nenhum metadado no .NET que nos informe se uma referência nula é aceitável ou não para uma API. A maioria das APIs irá gerar `ArgumentNullException` se não puderem lidar com um argumento `null`. Isso é problemático, pois o tratamento de exceções Objective-C é algo melhor evitado.

Como não podemos gerar anotações de nulidade precisas nos arquivos de cabeçalho e deseja minimizar as exceções gerenciadas, por padrão, os argumentos não nulos (`NS_ASSUME_NONNULL_BEGIN`) e adicionariam algum específico, quando a precisão é possível, anotações de nulidade.

### <a name="bitcode-ios"></a>BitCode (iOS)

Atualmente, a inserção do .NET não dá suporte a BitCode no iOS, que está habilitado para alguns modelos de projeto do Xcode. Isso terá que ser desabilitado para vincular as estruturas geradas com êxito.

* Para iOS, o BitCode é opcional para enviar aplicativos para o AppStore da Apple. O Xamarin. iOS não oferece suporte a ele para iOS, pois o BitCode gerado é "assembly embutido". Isso não oferece nenhum benefício na plataforma iOS porque não pode ser otimizado no lado do servidor, mas torna binários maiores e tempos de compilação mais longos.

* Para tvOS e watchOS, o BitCode é necessário para enviar aplicativos para o AppStore da Apple. O Xamarin. iOS dá suporte a BitCode em tvOS (como "assembly embutido") e watchOS (como "LLVM/IR") para atender a esse requisito.

* Para macOS, o suporte a BitCode não é necessário no momento, nem tem suporte do Xamarin. Mac.

![Opção BitCode](images/ios-bitcode-option.png)
