---
title: macOS APIs para desenvolvedores do xamarin. Mac
description: Este documento descreve como ler os seletores de Objective-C e como encontrar seus métodos c# correspondentes em um aplicativo xamarin. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/02/2017
ms.openlocfilehash: 6dfaa3c7bf988228bfbacefe7c8e7268edc8117a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994306"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS APIs para desenvolvedores do xamarin. Mac

## <a name="overview"></a>Visão geral

Na maior parte do seu tempo de desenvolvimento com o xamarin. Mac, você pode pensar, ler e escrever em c# sem muita preocupação com as APIs do Objective-C subjacente. No entanto, às vezes, você será necessário para ler a documentação da API da Apple, traduzir uma resposta de estouro de pilha para uma solução para o seu problema ou comparado com um exemplo existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Lendo suficiente Objective-C para ser perigosos

Às vezes, será necessário ler uma definição de Objective-C ou chamada de método e convertê-la para o método c# equivalente. Vamos dar uma olhada em uma definição de função do Objective-C e separar as partes. Esse método (um *seletor* em Objective-C) podem ser encontrados no `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

A declaração pode ser lido para a esquerda para a direita:

- O `-` prefixo significa que é um método de instância (não estático). + significa que ele é um método de classe (estático)
- `(BOOL)` é o tipo de retorno (bool em c#)
- `canDragRowsWithIndexes` é a primeira parte do nome.
- `(NSIndexSet *)rowIndexes` é o primeiro parâmetro e com ele tem o tipo. O primeiro parâmetro está no formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` é o segundo parâmetro e seu tipo. Todo parâmetro após a primeira é o formato: `selectorPart:(Type) pararmName`
- O nome completo do seletor mensagem é: `canDragRowsWithIndexes:atPoint:`. Observação o `:` final – é importante.
- A associação do xamarin. Mac c# real é: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Essa invocação seletor pode ser lido da mesma maneira:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- A instância `v` está tendo seu `canDragRowsWithIndexes:atPoint` seletor chamado com dois parâmetros: `set` e `point`, transmitida.
- No c#, a invocação de método tem esta aparência: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Localizando o membro de c# para um determinado seletor

Agora que você localizou o seletor de Objective-C, que você precisa invocar, a próxima etapa é mapeamento que para o membro equivalente em C#. Há quatro abordagens que você pode tentar (continuando com o `NSTableView CanDragRows` exemplo):

1. Use a lista de preenchimento automático para verificar rapidamente para algo de mesmo nome. Como sabemos que ele é uma instância de `NSTableView` você pode digitar:

    - `NSTableView x;`
    - `x.` [ctrl + espaço se a lista não aparecer).
    - `CanDrag` [Inserir]
    - Clique no método, vá para a declaração para abrir o navegador de Assembly em que você pode comparar o `Export` de atributo para o seletor em questão

2. A associação de classe inteira de pesquisa. Como sabemos que ele é uma instância de `NSTableView` você pode digitar:

    - `NSTableView x;`
    - Clique com botão direito `NSTableView`, vá para a declaração para o navegador de Assembly
    - Pesquise o seletor em questão

3. Você pode usar o [documentação online da API do xamarin. Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Miguel fornece uma exibição de "Lendária pedra" das APIs Xamarin.Mac [aqui](http://tirania.org/tmp/rosetta.html) que você pode pesquisar por meio de uma determinada API. Se sua API não é AppKit ou macOS específico, você pode encontrá-lo lá.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
