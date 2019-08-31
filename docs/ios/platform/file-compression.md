---
title: Compactação de arquivo no Xamarin. iOS
description: Este documento descreve como trabalhar com a API libcompression no Xamarin. iOS. Ele aborda os diferentes algoritmos de replanação, de replanação e com suporte.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: bcc63aa4e1926f5502d571bf47c83b0c8ea7e429
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199527"
---
# <a name="file-compression-in-xamarinios"></a>Compactação de arquivo no Xamarin. iOS

Os aplicativos Xamarin destinados ao iOS 9,0 ou ao macOS 10,11 (e mais recente) podem usar a _estrutura_ de compactação para compactar (codificar) e descompactar (decodificar) dados. O Xamarin. iOS fornece essa estrutura após a API de fluxo. A estrutura de compactação permite que os desenvolvedores interajam com os dados compactados e descompactados como se fossem fluxos normais sem a necessidade de usar retornos de chamada ou delegados.

A estrutura de compactação fornece suporte para os seguintes algoritmos:

* LZ4
* LZ4 bruto
* Lzfse
* Lzma
* Zlib

O uso da estrutura de compactação permite que os desenvolvedores realizem operações de compactação sem nenhuma biblioteca ou NuGets de terceiros. Isso reduz as dependências externas e garante que as operações de compactação terão suporte em todas as plataformas (desde que atendam aos requisitos mínimos do sistema operacional).

## <a name="general-file-decompression"></a>Descompactação de arquivo geral

A estrutura de compactação usa uma API de fluxo em Xamarin. iOS e Xamarin. Mac. Essa API significa que, para compactar dados, o desenvolvedor pode usar os padrões normais usados em outras APIs de e/s no .NET. O exemplo a seguir mostra como descompactar dados com a estrutura de compactação, que é semelhante à API encontrada `System.IO.Compression.DeflateStream` na API:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

O `CompressionStream` implementa a `IDisposable` interface, como a `System.IO.Streams`outra, para que os desenvolvedores devam garantir que os recursos sejam liberados quando não forem mais necessários.

## <a name="general-file-compression"></a>Compactação de arquivo geral

A API de compactação também permite que os desenvolvedores compactem dados seguindo a mesma API. Os dados podem ser compactados usando um dos algoritmos fornecidos indicados no `CompressionAlgorithm` enumerador.

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>Suporte assíncrono

O `CompressionStream` oferece suporte a todas as operações assíncronas com `System.IO.DeflateStream`suporte do, o que significa que os desenvolvedores podem usar a palavra-chave Async para executar as operações de compactação/descompactação sem bloquear o thread da interface do usuário.
