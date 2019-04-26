---
title: Tipos nativos para iOS e macOS
description: Este documento descreve como a API unificada do Xamarin mapeia tipos do .NET em tipos nativos 32 bits e 64 bits, conforme necessário com base na arquitetura de destino de compilação.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: fc2b91a9265fcf09e4f58d5de27a1fdef9350b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199618"
---
# <a name="native-types-for-ios-and-macos"></a>Tipos nativos para iOS e macOS

Mac e iOS APIs usar tipos de dados específicos de arquitetura que são sempre de 32 bits em plataformas de 32 bits e 64 bits em plataformas de 64 bits.

Por exemplo, o Objective-C mapeia o `NSInteger` tipo de dados `int32_t` em sistemas de 32 bits e, ao `int64_t` em sistemas de 64 bits.

Para corresponder a esse comportamento, em nossa API unificada, estamos substituindo os usos anteriores `int` (que no .NET é definida como sempre sendo `System.Int32`) para um novo tipo de dados: `System.nint`. Você pode pensar "n" como o que significa "nativo", para que o inteiro nativo digitar da plataforma.

Com esses novos tipos de dados, o mesmo código-fonte é compilado para arquiteturas de 32 bits e 64 bits, dependendo de seus sinalizadores de compilação.

## <a name="new-data-types"></a>Novos tipos de dados

A tabela a seguir mostra as alterações em nossos tipos de dados para corresponder a esse novo mundo de 32/64 bits:

|Tipo nativo|tipo de suporte de 32 bits|tipo de suporte de 64 bits|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Optamos por esses nomes para permitir que seu C# código para procurar mais ou menos da mesma forma que ele teria a aparência hoje mesmo.

### <a name="implicit-and-explicit-conversions"></a>Conversões implícitas e explícitas

O design de novos tipos de dados destina-se a permitir um único C# arquivo de origem usar naturalmente o armazenamento de bit de 32 ou 64 dependendo da plataforma de host e as configurações de compilação.

Isso exigia a criação de um conjunto de conversões implícitas e explícitas de e para os tipos de dados específicos da plataforma para os tipos de dados de ponto flutuante e integral do .NET.

Operadores de conversões implícitas são fornecidos quando não há nenhuma possibilidade de perda de dados (valores de 32 bits que estão sendo armazenados em um espaço de 64 bits).

Operadores de conversões explícitas são fornecidas quando há um potencial perda de dados (o valor de 64 bits está sendo armazenado em um local de armazenamento de 32 ou potencialmente 32).

 `int`, `uint` e `float` são todas implicitamente conversível para `nint`, `nuint` e `nfloat` como 32 bits sempre será melhor em 32 ou 64 bits.

 `nint`, `nuint` e `nfloat` são todas implicitamente conversível para `long`, `ulong` e `double` 32 ou 64 valores de bit sempre se ajustarem no armazenamento de 64 bits.

Você deve usar conversões explícitas de `nint`, `nuint` e `nfloat` em `int`, `uint` e `float` , pois os tipos nativos podem conter 64 bits do armazenamento.

Você deve usar conversões explícitas de `long`, `ulong` e `double` em `nint`, `nuint` e `nfloat` , pois os tipos nativos talvez só seja possível manter os 32 bits de armazenamento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Os tipos de dados de ponto, o tamanho e o retângulo que são usados com CoreGraphics usam 32 ou 64 bits, dependendo do dispositivo em que são executados.  Quando estamos vinculado originalmente as APIs de Mac e iOS usamos as estruturas de dados existentes que ocorreram corresponder os tamanhos da plataforma do host (os tipos de dados em `System.Drawing`).

Ao mover para o **unificado**, você precisará substituir instâncias da `System.Drawing` com suas `CoreGraphics` equivalentes, conforme mostrado na tabela a seguir:

|Tipo antigo no System. Drawing|Novo CoreGraphics de tipo de dados|Descrição|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Mantém informações do retângulo de ponto de flutuante.|
|`SizeF`|`CGSize`|Informações de tamanho (largura, altura) de ponto de suspensões flutuante|
|`PointF`|`CGPoint`|Contém um ponto flutuante, (X, Y) de informações de ponto|

As antigo flutuações de tipos usados dados para armazenar os elementos das estruturas de dados, enquanto o novo um usa `System.nfloat`.

## <a name="related-links"></a>Links relacionados

- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
