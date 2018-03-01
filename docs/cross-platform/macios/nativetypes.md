---
title: Tipos nativos
ms.topic: article
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: b78ade19efed92ab3b2d8ba790f2d7334472bab4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="native-types"></a>Tipos nativos

No núcleo da diferença, Mac e iOS APIs use um tipos de dados específicos de arquitetura sempre 32 bits em plataformas de 32 bits e 64 bits em plataformas de 64 bits.

Por exemplo, Objective-C mapeia o `NSInteger` tipo de dados `int32_t` em sistemas de 32 bits e para `int64_t` em sistemas de 64 bits.

Para corresponder a esse comportamento, em nossa API unificada, estamos substituindo os usos anteriores `int` (que no .NET é definida como sendo sempre `System.Int32`) para um novo tipo de dados: `System.nint`.  Você pode pensar "n" como significado "nativo" para digitar o número inteiro nativo da plataforma.

Com esses novos tipos de dados, o mesmo código de origem é compilado para 32 bits, 32 bits e 64 bits ou 64 bits, dependendo dos sinalizadores de compilação.

## <a name="new-data-types"></a>Novos tipos de dados

A tabela a seguir mostra as alterações em nossos tipos de dados para corresponder a esse novo mundo de 32/64 bits:

<table>
        <tr>
            <th>Tipo nativo</th>
            <th>tipo de suporte de 32 bits</th> 
            <th>tipo de suporte de 64 bits</th>
        </tr>
        <tr>
            <td><code>System.nint</code></td>
        <td><code>System.Int32</code> (<code>int</code>)</td>
        <td><code>System.Int64</code> (<code>long</code>)</td>
        </tr>
        <tr>
            <td><code>System.nuint</code></td>
        <td><code>System.UInt32</code> (<code>uint</code>)</td>
        <td><code>System.UInt64</code> (<code>ulong</code>)</td>
        </tr>
        <tr>
            <td><code>System.nfloat</code></td>
        <td><code>System.Single</code> (<code>float</code>)</td>
        <td><code>System.Double</code> (<code>double</code>)</td>
        </tr>
    </table>

Escolhemos a esses nomes para permitir que seu código c# pesquisar mais ou menos da mesma forma que seria hoje.

### <a name="implicit-and-explicit-conversions"></a>Conversões implícitas e explícitas

O design de novos tipos de dados destina-se a permitir que um único arquivo fonte c# naturalmente usar armazenamento de bit de 32 ou 64 dependendo da plataforma do host e as configurações de compilação.

Isso necessário para criar um conjunto de conversões implícitas e explícitas de e para os tipos de dados específicos de plataforma para os tipos de dados de ponto flutuante e integral do .NET.

Operadores de conversões implícitas são fornecidos quando não houver nenhuma possibilidade de perda de dados (valores de 32 bits está sendo armazenados em um espaço de 64 bits).

Operadores de conversões explícitas são fornecidos quando há uma possibilidade de perda de dados (valor de 64 bits está sendo armazenado em um local de armazenamento de 32 ou potencialmente 32).

 `int`, `uint` e `float` são todos implicitamente conversível em `nint`, `nuint` e `nfloat` como 32 bits sempre se encaixe em 32 ou 64 bits.

 `nint`, `nuint` e `nfloat` são todos implicitamente conversível em `long`, `ulong` e `double` 32 ou 64 valores de bit sempre se ajustarem no armazenamento de 64 bits.

Você deve usar conversões explícitas de `nint`, `nuint` e `nfloat` em `int`, `uint` e `float` desde que os tipos nativos podem manter 64 bits de armazenamento.

Você deve usar conversões explícitas de `long`, `ulong` e `double` em `nint`, `nuint` e `nfloat` como os tipos nativos só podem ser capazes de armazenar 32 bits de armazenamento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Os tipos de dados de ponto, o tamanho e o retângulo que são usados com CoreGraphics usam 32 ou 64 bits dependendo do dispositivo em que estão sendo executados.  Quando é vinculado originalmente o iOS e Mac APIs usamos estruturas de dados existentes que ocorreram corresponder os tamanhos da plataforma de host (os tipos de dados em `System.Drawing`).

Ao mover para **unificada**, você precisará substituir instâncias da `System.Drawing` com seus `CoreGraphics` equivalentes, conforme mostrado na tabela a seguir:

<table>
        <tr>
            <th>Tipo antigo no System. Drawing</th>
            <th>Novo CoreGraphics de tipo de dados</th> 
            <th>Descrição</th>
        </tr>
        <tr>
        <td><code>RectangleF</code></td>
        <td><code>CGRect</code></td>
        <td>Mantém flutuante informações do ponto de retângulo.  </td>
        </tr>
        <tr>
        <td><code>SizeF</code></td>
        <td><code>CGSize</code></td>
        <td>Informações sobre o tamanho (largura, altura) do ponto de mantém flutuante</td>
        </tr>
        <tr>
        <td><code>PointF</code></td>
        <td><code>CGPoint</code></td>
        <td>Contém um ponto flutuante, do ponto de informações (X, Y)</td>
        </tr>
    </table>

As antigo flutuações de tipos usados de dados para armazenar os elementos das estruturas de dados, enquanto o novo usa `System.nfloat`.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferenças de API unificada do vs clássico](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
