---
title: .NET Embedding
description: A incorporação do .net permite que seu códigoC#.NET F#existente (, e outros) seja consumido pelo código escrito em outras linguagens de programação.
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: af068e5a09cc11eec33508a4f2eb33186168aae6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290223"
---
# <a name="net-embedding"></a>.NET Embedding

![Visualizar](~/media/shared/preview.png)

A incorporação do .net permite que seu códigoC#.NET F#existente (, e outros) seja consumido por outras linguagens de programação e em vários ambientes diferentes.

Isso significa que, se você tiver uma biblioteca .NET que deseja usar de seu aplicativo iOS existente, poderá fazer isso.   Ou, se você quiser vinculá-lo a C++ uma biblioteca nativa, também poderá fazer isso.   Ou consumir código .NET do Java.

A incorporação .NET é baseada no projeto de software livre [Embeddinator-4000](https://github.com/mono/Embeddinator-4000) .

## <a name="environments-and-languages"></a>Ambientes e idiomas

A ferramenta está ciente do ambiente que usará, bem como a linguagem que irá consumi-lo.   Por exemplo, a plataforma iOS não permite compilação JIT (just-in-time), portanto, a incorporação .NET compilará estaticamente seu código .NET em código nativo que pode ser usado no iOS.  Outros ambientes permitem a compilação JIT e, nesses ambientes, optamos por compilação JIT.

Ele dá suporte a vários consumidores de idioma, portanto, ele superfícies o código .NET como código idiomática no idioma de destino.   Esta é a lista de idiomas com suporte no momento:

- [**Objective-c**](objective-c/index.md) – mapeando o .net para as APIs idiomática Objective-c
- [**Java**](android/index.md) – mapeando .net para APIs Java idiomática
- [**C**](get-started/c.md) – mapeando o .net para o orientado a objeto como APIs C

Mais idiomas serão fornecidos posteriormente.

## <a name="getting-started"></a>Guia de Introdução

Para começar, verifique um dos nossos guias para cada um dos idiomas com suporte no momento:

- [**Objective-C**](get-started/objective-c/index.md) – abrange MacOS e Ios
- [**Java**](get-started/java/index.md) – abrange MacOS e Android
- [**C**](get-started/c.md) – abrange a linguagem c em plataformas de desktop

## <a name="related-links"></a>Links relacionados

- [Embeddinator-4000 no GitHub](https://github.com/mono/Embeddinator-4000)
