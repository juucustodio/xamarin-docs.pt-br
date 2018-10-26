---
title: .NET Embedding
description: 'A inserção de .NET permite que seu código existente do .NET (c#, F # e outras pessoas) a serem consumidos pelo código escrito em outras linguagens de programação.'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 23233ea8b06e0db580ba99edf2705e3dae5b931f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107112"
---
# <a name="net-embedding"></a>.NET Embedding

![Visualizar](~/media/shared/preview.png)

A inserção de .NET permite que seu código existente do .NET (c#, F # e outras pessoas) a ser consumida de outras linguagens de programação e em vários ambientes diferentes.

Isso significa que, se você tiver uma biblioteca .NET que você deseja usar em seu aplicativo iOS existente, você pode fazer isso.   Ou, se você deseja vinculá-lo com uma biblioteca C++ nativa, você também pode fazer isso.   Ou consumir código .NET do Java.

Incorporação do .NET se baseia a [Embeddinator 4000](https://github.com/mono/Embeddinator-4000) projeto de software livre.

## <a name="environments-and-languages"></a>Ambientes e linguagens

A ferramenta é cientes do ambiente, ele usará, bem como a linguagem que irá consumi-lo.   Por exemplo, a plataforma iOS não permite just-in-time (compilação JIT), para que a incorporação do .NET estaticamente compilar seu código .NET em código nativo que pode ser usado no iOS.  Outros ambientes permitem a compilação JIT e nesses ambientes, optamos por compilação JIT.

Ele dá suporte a vários consumidores de linguagem, portanto, ele revela o código .NET como expressões idiomática código no idioma de destino.   Esta é a lista de idiomas com suporte no momento:

- [**Objective-C** ](objective-c/index.md) – mapeamento de .NET para expressões idiomáticas APIs do Objective-C
- [**Java** ](android/index.md) – mapeamento de .NET para expressões idiomáticas APIs de Java
- [**C** ](get-started/c.md) – mapeando .NET para orientada a objeto, como APIs de C

Mais idiomas virão posteriormente.

## <a name="getting-started"></a>Guia de Introdução

Para começar, consulte um dos nossos guias para cada um dos idiomas com suporte no momento:

- [**Objective-C** ](get-started/objective-c/index.md) – aborda iOS e macOS
- [**Java** ](get-started/java/index.md) – aborda macOS e Android
- [**C** ](get-started/c.md) – aborda a linguagem C em plataformas de desktop

## <a name="related-links"></a>Links relacionados

- [Embeddinator-4000 no GitHub](https://github.com/mono/Embeddinator-4000)
