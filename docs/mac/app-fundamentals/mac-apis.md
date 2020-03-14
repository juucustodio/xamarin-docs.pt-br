---
title: APIs do macOS para desenvolvedores do Xamarin. Mac
description: Este documento descreve como ler seletores Objective-C e como localizar seus métodos correspondentes C# em um aplicativo Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: cd427d13bb79fd31e1e814726aaaf61788ae10ec
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304985"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>APIs do macOS para desenvolvedores do Xamarin. Mac

## <a name="overview"></a>Visão geral

Por muito tempo desenvolvendo com o Xamarin. Mac, você pode imaginar, ler e escrever C# sem muita preocupação com as APIs de Objective-C subjacentes. No entanto, às vezes você precisará ler a documentação da API da Apple, traduzir uma resposta de Stack Overflow para uma solução para seu problema ou comparar com um exemplo existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Lendo o objetivo-C suficiente para ser perigoso

Às vezes, será necessário ler uma definição ou chamada de método Objective-C e traduzi-la para C# o método equivalente. Vamos dar uma olhada em uma definição de função Objective-C e dividir as peças. Este método (um *seletor* em Objective-C) pode ser encontrado em `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

A declaração pode ser lida da esquerda para a direita:

- O prefixo `-` significa que ele é um método de instância (não estático). + significa que ele é um método de classe (estático)
- `(BOOL)` é o tipo de retorno (bool C#em)
- `canDragRowsWithIndexes` é a primeira parte do nome.
- `(NSIndexSet *)rowIndexes` é o primeiro param e com seu tipo. O primeiro parâmetro está no formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` é o segundo param e seu tipo. Cada parâmetro após o primeiro é o formato: `selectorPart:(Type) pararmName`
- O nome completo desse seletor de mensagem é: `canDragRowsWithIndexes:atPoint:`. Observe o `:` no final-é importante.
- A associação do Xamarin. C# Mac real é: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Essa invocação de seletor pode ser lida da mesma maneira:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- A instância `v` está tendo seu seletor de `canDragRowsWithIndexes:atPoint` chamado com dois parâmetros, `set` e `point`, passados.
- No C#, a invocação do método tem esta aparência: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Encontrando o C# membro para um determinado seletor

Agora que você encontrou o seletor Objective-C que precisa invocar, a próxima etapa é mapear isso para C# o membro equivalente. Há quatro abordagens que você pode tentar (continuando com o exemplo de `NSTableView CanDragRows`):

1. Use a lista de preenchimento automático para verificar rapidamente se há algo com o mesmo nome. Como sabemos que é uma instância do `NSTableView` você pode digitar:

    - `NSTableView x;`
    - `x.` [Ctrl + Space se a lista não for exibida).
    - `CanDrag` [Enter]
    - Clique com o botão direito do mouse no método, vá para declaração para abrir o navegador de assembly, no qual você pode comparar o atributo `Export` ao seletor em questão

2. Pesquise toda a associação de classe. Como sabemos que é uma instância do `NSTableView` você pode digitar:

    - `NSTableView x;`
    - Clique com o botão direito do mouse em `NSTableView`, acesse a declaração para o navegador de assembly
    - Pesquisar o seletor em questão

3. Você pode usar a [documentação online da API do Xamarin. Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. O Miguel fornece uma exibição de "lendária Stone" das APIs do Xamarin. Mac [aqui](https://tirania.org/tmp/rosetta.html) que você pode pesquisar por uma determinada API. Se sua API não for AppKit ou macOS específica, você poderá encontrá-la lá.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
