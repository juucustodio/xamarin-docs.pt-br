---
title: Tipos nativos para iOS e macOS
description: Este documento descreve como o API Unificada do Xamarin mapeia tipos .NET para tipos nativos de 32 bits e 64 bits, conforme necessário com base na arquitetura de destino da compilação.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: conceptdev
ms.author: crdun
ms.date: 01/25/2016
ms.openlocfilehash: f370bc816fa72f94af8dae32ac295448f858dbb8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290802"
---
# <a name="native-types-for-ios-and-macos"></a>Tipos nativos para iOS e macOS

As APIs do Mac e do iOS usam tipos de dados específicos da arquitetura que são sempre 32 bits em plataformas de 32 bits e 64 bits em plataformas de 64 bits.

Por exemplo, Objective-C mapeia `NSInteger` o tipo de `int32_t` dados para sistemas de 32 bits e `int64_t` para sistemas de 64 bits.

Para corresponder a esse comportamento, em nossa API unificada, estamos substituindo os `int` usos anteriores de (que, no .net, `System.Int32`é definido como sempre sendo) para `System.nint`um novo tipo de dados:. Você pode considerar o "n" como significa "nativo", portanto, o tipo inteiro nativo da plataforma.

Com esses novos tipos de dados, o mesmo código-fonte é compilado para arquiteturas de 32 bits e 64 bits, dependendo dos seus sinalizadores de compilação.

## <a name="new-data-types"></a>Novos tipos de dados

A tabela a seguir mostra as alterações em nossos tipos de dados para corresponder a esse novo mundo de 32/64 bits:

|Tipo nativo|tipo de suporte de 32 bits|tipo de suporte de 64 bits|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Escolhemos esses nomes para permitir que C# seu código pareça mais ou menos da mesma maneira que ele pareceria hoje.

### <a name="implicit-and-explicit-conversions"></a>Conversões implícitas e explícitas

O design dos novos tipos de dados destina-se a permitir que C# um único arquivo de origem use naturalmente o armazenamento de 32 ou 64 bits dependendo da plataforma de host e das configurações de compilação.

Isso nos exigia criar um conjunto de conversões implícitas e explícitas de e para os tipos de dados específicos da plataforma para os tipos de dados de ponto flutuante e integral .NET.

Operadores de conversões implícitas são fornecidos quando não há possibilidade de perda de dados (valores de 32 bits sendo armazenados em um espaço de 64 bits).

Operadores de conversões explícitas são fornecidos quando há um potencial para perda de dados (o valor de 64 bits está sendo armazenado em um local de armazenamento 32 ou potencialmente 32).

`int`, `uint` `nint`e `float` são todos conversíveis implicitamente para `nuint` , `nfloat` e como 32 bits se ajustarão sempre em 32 ou 64 bits.

`nint`, `nuint` `long`e `nfloat` são todos conversíveis implicitamente para `ulong` , `double` e como os valores de bits 32 ou 64 serão sempre ajustados no armazenamento de 64 bits.

Você deve usar conversões explícitas de `nint` `nuint` e `nfloat` para `int`, `uint` e `float` como os tipos nativos podem conter 64 bits de armazenamento.

Você deve usar conversões explícitas de `long` `ulong` e `double` para `nint`, `nuint` e `nfloat` como os tipos nativos podem ser capazes de conter apenas 32 bits de armazenamento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Os tipos de dados ponto, tamanho e retângulo que são usados com CoreGraphics usam 32 ou 64 bits, dependendo do dispositivo em que estão sendo executados.  Quando vinculamos originalmente as APIs de iOS e Mac, usamos as estruturas de dados existentes que ocorreram para corresponder os tamanhos da plataforma de host ( `System.Drawing`os tipos de dados no).

Ao mudar para **unificado**, você precisará substituir instâncias `System.Drawing` do por `CoreGraphics` suas contrapartes, conforme mostrado na tabela a seguir:

|Tipo antigo no sistema. Drawing|Novo tipo de dados CoreGraphics|Descrição|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Mantém informações de retângulo de ponto flutuante.|
|`SizeF`|`CGSize`|Contém informações de tamanho de ponto flutuante (largura, altura)|
|`PointF`|`CGPoint`|Mantém um ponto flutuante, informações de ponto (X, Y)|

Os tipos de dados antigos usados flutuam para armazenar os elementos das estruturas de dados, enquanto o novo usa `System.nfloat`.

## <a name="related-links"></a>Links relacionados

- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
