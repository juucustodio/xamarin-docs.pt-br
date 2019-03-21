---
title: Compactação de arquivo no xamarin. IOS
description: Este documento descreve como trabalhar com a API libcompression no xamarin. IOS. Ele aborda deflating, aumentando, e suporte a diferentes algoritmos.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: f7a1df65047fd8040dd40e9f7f057d6bfe6dea61
ms.sourcegitcommit: 4c97f5d73be7eb2da153a85183be4258b6b11ca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58290918"
---
# <a name="file-compression-in-xamarinios"></a>Compactação de arquivo no xamarin. IOS

Aplicativos Xamarin direcionado para o iOS 9.0 ou macOS 10.11 (e mais recente) podem usar o _Framework compactação_ para compactar (codificar) e descompactar (decodificação) dados. Xamarin. IOS fornece essa estrutura seguindo a API do Stream. A estrutura de compactação permite aos desenvolvedores interagir com a compactar e descompactar dados como se fossem fluxos normais sem a necessidade de usar retornos de chamada ou delegados.

O framework de compactação fornece suporte para os seguintes algoritmos:

* LZ4
* LZ4 bruto
* Lzfse
* Lzma
* Zlib

Usando a estrutura de compactação permite aos desenvolvedores executar operações de compactação sem NuGets ou bibliotecas de terceiros. Isso reduz as dependências externas e garante que as operações de compactação terá suporte em todas as plataformas (desde que eles atendem aos requisitos mínimos de sistema operacional).

## <a name="general-file-decompression"></a>Descompactação de arquivos gerais

A estrutura de compactação usa uma API de transmissão no xamarin. IOS e xamarin. Mac. Essa API significa que, para compactar os dados, o desenvolvedor pode usar os padrões normais usados em outras APIs de e/s do .NET. O exemplo a seguir mostra como descompactar dados com a estrutura de compactação, que é semelhante para a API encontrada no `System.IO.Compression.DeflateStream` API:

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

O `CompressionStream` implementa o `IDisposable` interface, como outro `System.IO.Streams`, portanto, os desenvolvedores devem garantir que os recursos são liberados depois que eles não são mais necessários.

## <a name="general-file-compression"></a>Compactação de arquivos gerais

A API de compactação também permite que os desenvolvedores compactar os dados a seguir a mesma API. Dados podem ser compactados usando um dos algoritmos fornecidos indicados no `CompressionAlgorithm` enumerador.

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

O `CompressionStream` dá suporte a todas as operações assíncronas que são compatíveis com o `System.IO.DeflateStream`, que significa que os desenvolvedores podem usar a palavra-chave async para executar as operações de compactar/descompactar sem bloquear o thread de interface do usuário.