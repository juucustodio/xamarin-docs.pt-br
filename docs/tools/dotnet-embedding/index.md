---
title: "Inserção de .NET"
description: "Inserindo .NET permite que seu código existente do .NET (c#, F # e outros) para ser consumido de outras linguagens de programação"
ms.topic: article
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 29c34ede0b59620b8951109f8571a08a960182d1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="net-embedding"></a>Inserção de .NET

![Visualizar](~/media/shared/preview.png)

Inserindo .NET permite que seu código existente do .NET (c#, F # e outros) a serem consumidos em outras linguagens de programação e em vários ambientes diferentes.

Isso significa que, se você tiver uma biblioteca .NET que você deseja usar de seu aplicativo iOS existente, você pode fazer isso.   Ou, se você quiser vinculá-lo a uma biblioteca C++ nativo, você também pode fazer isso.   Ou consumir código .NET do Java.

## <a name="environments-and-languages"></a>Ambientes e idiomas

A ferramenta é cientes o ambiente que será usado, bem como o idioma que irá consumi-lo.   Por exemplo, a plataforma iOS não permite a compilação do just-in-time (JIT), para que o embeddinator estaticamente compilará o código .NET em código nativo que pode ser usado no iOS.  Outros ambientes permitem a compilação JIT, e esses ambientes, podemos aceitar a compilação JIT.

Ele oferece suporte a vários consumidores de idioma, ele reproduz o código .NET como código idiomática no idioma de destino.   Esta é a lista de idiomas com suporte no momento:

- [**Objective-C** ](objective-c/index.md) – mapeando .NET para APIs de idiomática Objective-C.
- [**Java** ](android/index.md) – mapeando .NET para APIs idiomática de Java.
- **C**: o mapeamento do .NET para um orientada a objeto como as APIs de C.

Mais idiomas virão posteriormente.

## <a name="getting-started"></a>Guia de Introdução

Para começar, verifique se um dos nossos guias para cada um dos idiomas com suporte no momento:

- [**Objective-C** ](get-started/objective-c/index.md) – abrange macOS e iOS.
- [**Java** ](get-started/java/index.md) – abrange macOS e Android.
- [**C** ](get-started/c.md) – aborda linguagem C em plataformas de área de trabalho.


## <a name="related-links"></a>Links relacionados

- [Embeddinator-4000 on GitHub](https://github.com/mono/Embeddinator-4000)
