---
title: Tipos nativos para iOS e macOS
description: Este documento descreve como o API Unificada do Xamarin mapeia tipos .NET para tipos nativos de 32 bits e 64 bits, conforme necessário com base na arquitetura de destino da compilação.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: 1168dfe0f2120e87c57b46011caba5e7a8a0c020
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015489"
---
# <a name="native-types-for-ios-and-macos"></a>Tipos nativos para iOS e macOS

As APIs do Mac e do iOS usam tipos de dados específicos da arquitetura que são sempre 32 bits em plataformas de 32 bits e 64 bits em plataformas de 64 bits.

Por exemplo, Objective-C mapeia o tipo de dados `NSInteger` para `int32_t` em sistemas de 32 bits e para `int64_t` em sistemas de 64 bits.

Para corresponder a esse comportamento, em nossa API unificada, estamos substituindo os usos anteriores de `int` (que, no .NET, é definido como sempre sendo `System.Int32`) para um novo tipo de dados: `System.nint`. Você pode considerar o "n" como significa "nativo", portanto, o tipo inteiro nativo da plataforma.

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

`int`, `uint` e `float` são implicitamente conversíveis para `nint`, `nuint` e `nfloat` como 32 bits se ajustarão sempre em 32 ou 64 bits.

`nint`, `nuint` e `nfloat` são implicitamente conversíveis para `long`, `ulong` e `double` como os valores de bits 32 ou 64 serão sempre ajustados no armazenamento de 64 bits.

Você deve usar conversões explícitas de `nint`, `nuint` e `nfloat` em `int`, `uint` e `float`, já que os tipos nativos podem conter 64 bits de armazenamento.

Você deve usar conversões explícitas de `long`, `ulong` e `double` em `nint`, `nuint` e `nfloat`, já que os tipos nativos podem ser capazes de conter apenas 32 bits de armazenamento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Os tipos de dados ponto, tamanho e retângulo que são usados com CoreGraphics usam 32 ou 64 bits, dependendo do dispositivo em que estão sendo executados.  Quando vinculamos originalmente as APIs de iOS e Mac, usamos as estruturas de dados existentes que ocorreram para corresponder os tamanhos da plataforma de host (os tipos de dados no `System.Drawing`).

Ao mudar para **unificado**, você precisará substituir instâncias de `System.Drawing` por suas contrapartes `CoreGraphics`, conforme mostrado na tabela a seguir:

|Tipo antigo no sistema. Drawing|Novo tipo de dados CoreGraphics|Descrição|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Mantém informações de retângulo de ponto flutuante.|
|`SizeF`|`CGSize`|Contém informações de tamanho de ponto flutuante (largura, altura)|
|`PointF`|`CGPoint`|Mantém um ponto flutuante, informações de ponto (X, Y)|

Os tipos de dados antigos usados flutuam para armazenar os elementos das estruturas de dados, enquanto o novo usa `System.nfloat`.

## <a name="related-links"></a>Links relacionados

- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
