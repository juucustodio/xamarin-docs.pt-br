---
title: APIs do macOS para desenvolvedores do Xamarin. Mac
description: Este documento descreve como ler os seletores Objective-C e como localizar seus métodos C# correspondentes em um aplicativo Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: 67638a261cd9a6e8c356924d47ea4adb4eae6a80
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430997"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>APIs do macOS para desenvolvedores do Xamarin. Mac

## <a name="overview"></a>Visão geral

Por muito tempo desenvolvendo com o Xamarin. Mac, você pode imaginar, ler e escrever em C# sem muita preocupação com as APIs de Objective-C subjacentes. No entanto, às vezes você precisará ler a documentação da API da Apple, traduzir uma resposta de Stack Overflow para uma solução para seu problema ou comparar com um exemplo existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Lendo o objetivo-C suficiente para ser perigoso

Às vezes, será necessário ler uma definição ou chamada de método Objective-C e traduzi-la para o método C# equivalente. Vamos dar uma olhada em uma definição de função Objective-C e dividir as peças. Este método (um *seletor* em Objective-C) pode ser encontrado em `NSTableView` :

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

A declaração pode ser lida da esquerda para a direita:

- O `-` prefixo significa que ele é um método de instância (não estático). + significa que ele é um método de classe (estático)
- `(BOOL)` é o tipo de retorno (bool em C#)
- `canDragRowsWithIndexes` é a primeira parte do nome.
- `(NSIndexSet *)rowIndexes` é o primeiro param e com seu tipo. O primeiro parâmetro está no formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` é o segundo param e seu tipo. Cada parâmetro após o primeiro é o formato: `selectorPart:(Type) pararmName`
- O nome completo desse seletor de mensagem é: `canDragRowsWithIndexes:atPoint:` . Observe que `:` , no final, é importante.
- A ligação real do Xamarin. Mac C# é: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Essa invocação de seletor pode ser lida da mesma maneira:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- A instância `v` está tendo seu `canDragRowsWithIndexes:atPoint` seletor chamado com dois parâmetros `set` e `point` , passado.
- No C#, a invocação do método tem esta aparência: `x.CanDragRows (set, point);`

<a name="finding_selector"></a>

## <a name="finding-the-c-member-for-a-given-selector"></a>Encontrando o membro do C# para um determinado seletor

Agora que você encontrou o seletor Objective-C que precisa invocar, a próxima etapa é mapear isso para o membro C# equivalente. Há quatro abordagens que você pode tentar (continuando com o `NSTableView CanDragRows` exemplo):

1. Use a lista de preenchimento automático para verificar rapidamente se há algo com o mesmo nome. Como sabemos que é uma instância do `NSTableView` , você pode digitar:

    - `NSTableView x;`
    - `x.` [Ctrl + Space se a lista não for exibida).
    - `CanDrag` digita
    - Clique com o botão direito do mouse no método, vá para declaração para abrir o navegador de assembly no qual você pode comparar o `Export` atributo com o seletor em questão

2. Pesquise toda a associação de classe. Como sabemos que é uma instância do `NSTableView` , você pode digitar:

    - `NSTableView x;`
    - Clique com o botão direito do mouse `NSTableView` , vá para a declaração para o navegador de assembly
    - Pesquisar o seletor em questão

3. Você pode usar a [documentação online da API do Xamarin. Mac](/dotnet/api/?view=xamarinmac-3.0) .

4. O Miguel fornece uma exibição de "lendária Stone" das APIs do Xamarin. Mac [aqui](https://tirania.org/tmp/rosetta.html) que você pode pesquisar por uma determinada API. Se sua API não for AppKit ou macOS específica, você poderá encontrá-la lá.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->