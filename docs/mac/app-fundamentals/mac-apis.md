---
title: APIs do Mac
description: Este documento descreve como ler seletores Objective-C e como localizar seus métodos c# correspondentes.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/02/2017
ms.openlocfilehash: 0344fecb9a8d64a680bb11689f56cf074d952f4e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="mac-apis"></a>APIs do Mac

## <a name="overview"></a>Visão geral

Para a maior parte do seu tempo de desenvolvimento com Xamarin.Mac, você pode pensar, leitura e gravação em c# sem precisar se preocupar muito com as APIs Objective-C subjacente. No entanto, às vezes, você será necessário para ler a documentação da API da Apple, converter uma resposta de estouro de pilha para uma solução para o seu problema ou comparar a uma amostra existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Leitura suficiente Objective-C para ser perigoso

Às vezes, é necessário ler uma definição de Objective-C ou método chamar e convertê-la para o método equivalente do c#. Vamos dar uma olhada em uma definição de função Objective-C e dividir as partes. Esse método (um *seletor* em Objective-C) podem ser encontradas no `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

A declaração pode ser lido para a esquerda para a direita:

- O `-` prefixo significa que ele é um método de instância (não estático). + significa que ele é um método de classe (estático)
- `(BOOL)` é o tipo de retorno (bool em c#)
- `canDragRowsWithIndexes` é a primeira parte do nome.
- `(NSIndexSet *)rowIndexes` é o primeiro parâmetro e com ele tem tipo. O primeiro parâmetro está no formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` é o segundo parâmetro e seu tipo. Cada parâmetro após a primeira é o formato: `selectorPart:(Type) pararmName`
- O nome completo do seletor mensagem é: `canDragRowsWithIndexes:atPoint:`. Observe o `:` final - é importante.
- A associação Xamarin.Mac c# real é: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Esta chamada de seletor pode ser lido da mesma maneira:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- A instância `v` está tendo seu `canDragRowsWithIndexes:atPoint` seletor chamado com dois parâmetros, `set` e `point`, passado.
- No c#, a chamada do método tem esta aparência: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Localizando o membro c# de seletor determinado

Agora que você encontrou o seletor de Objective-C que é necessário chamar, a próxima etapa é mapear que para o membro equivalente do c#. Há quatro abordagens que você pode tentar (continuar com o `NSTableView CanDragRows` exemplo):

1. Use a lista de conclusão automática para verificar rapidamente algo de mesmo nome. Como sabemos que ele é uma instância de `NSTableView` você pode digitar:

    - `NSTableView x;`
    - `x.` [ctrl + espaço, se a lista não aparecer).
    - `CanDrag` [enter]
    - O método de atalho, vá para a declaração para abrir o navegador de Assembly em que você pode comparar a `Export` de atributo para o seletor em questão

2. Pesquise a associação de classe inteira. Como sabemos que ele é uma instância de `NSTableView` você pode digitar:

    - `NSTableView x;`
    - Clique com botão direito `NSTableView`, vá para a declaração de navegador do Assembly
    - Pesquise o seletor em questão

3. Você pode usar o [documentação on-line Xamarin.Mac API](https://developer.xamarin.com/api/root/monomac-lib/) .

4. Miguel fornece uma exibição de "Armazenar Roseta" das APIs Xamarin.Mac [aqui](http://tirania.org/tmp/rosetta.html) que você pode pesquisar por meio de uma API determinada. Se a sua API não é AppKit ou macOS específico, talvez seja lá.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
