---
title: .NET Embedding
description: 'Inserindo .NET permite que seu código existente do .NET (c#, F # e outros) para ser consumido pelo código escrito em outras linguagens de programação.'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 16f59498a49d10a43e04989136d8835bf78bd89d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793110"
---
# <a name="net-embedding"></a>.NET Embedding

![Visualizar](~/media/shared/preview.png)

Inserindo .NET permite que seu código existente do .NET (c#, F # e outros) a serem consumidos em outras linguagens de programação e em vários ambientes diferentes.

Isso significa que, se você tiver uma biblioteca .NET que você deseja usar de seu aplicativo iOS existente, você pode fazer isso.   Ou, se você quiser vinculá-lo a uma biblioteca C++ nativo, você também pode fazer isso.   Ou consumir código .NET do Java.

Inserindo .NET se baseia o [Embeddinator 4000](https://github.com/mono/Embeddinator-4000) projeto de software livre.

## <a name="environments-and-languages"></a>Ambientes e idiomas

A ferramenta é cientes o ambiente que será usado, bem como o idioma que irá consumi-lo.   Por exemplo, a plataforma iOS não permite a compilação do just-in-time (JIT), para que .NET inserindo estaticamente compilar o código do .NET em código nativo que pode ser usado no iOS.  Outros ambientes permitem que a compilação JIT e nesses ambientes, podemos aceitar compilação JIT.

Ele oferece suporte a vários consumidores de idioma, ele reproduz o código .NET como código idiomática no idioma de destino.   Esta é a lista de idiomas com suporte no momento:

- [**Objective-C** ](objective-c/index.md) – mapeando .NET para APIs de idiomática Objective-C
- [**Java** ](android/index.md) – mapeando .NET para APIs de Java idiomática
- [**C** ](get-started/c.md) – mapeamento .NET orientada a objeto como as APIs de C

Mais idiomas virão posteriormente.

## <a name="getting-started"></a>Guia de Introdução

Para começar, verifique se um dos nossos guias para cada um dos idiomas com suporte no momento:

- [**Objective-C** ](get-started/objective-c/index.md) – abrange macOS e iOS
- [**Java** ](get-started/java/index.md) – abrange macOS e Android
- [**C** ](get-started/c.md) – aborda linguagem C em plataformas de área de trabalho

## <a name="related-links"></a>Links relacionados

- [Embeddinator-4000 no GitHub](https://github.com/mono/Embeddinator-4000)
