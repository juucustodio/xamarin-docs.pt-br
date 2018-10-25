---
title: Associação de dados do xamarin. Forms
description: Associação de dados é a técnica de vinculação de propriedades de dois objetos para que as alterações em uma propriedade são refletidas automaticamente na outra propriedade. Associação de dados é parte integrante da arquitetura do aplicativo Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2018
ms.openlocfilehash: def97ab77781c7a7156d4c4178097184614f3e8b
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "35240347"
---
# <a name="xamarinforms-data-binding"></a>Associação de dados do xamarin. Forms

_Associação de dados é a técnica de vinculação de propriedades de dois objetos para que as alterações em uma propriedade são refletidas automaticamente na outra propriedade. Associação de dados é parte integrante da arquitetura do aplicativo Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>O problema de vinculação de dados

Um aplicativo xamarin. Forms consiste em uma ou mais páginas, geralmente, cada um deles contém vários objetos de interface do usuário chamados *modos de exibição*. Uma das principais tarefas do programa é manter essas exibições sincronizadas e para controlar os vários valores ou seleções que eles representam. Muitas vezes os modos de exibição representam valores de uma fonte de dados subjacente e, em seguida, o usuário manipula esses modos de exibição para alterar esses dados. Quando o modo de exibição é alterada, os dados subjacentes devem refletir essa alteração, e da mesma forma, quando os dados subjacentes for alterado, essa alteração deve ser refletida no modo de exibição.

Para lidar com esse trabalho com êxito, o programa deve ser notificado das alterações nessas exibições ou os dados subjacentes. A solução comum é definir os eventos que sinalizam quando ocorre uma alteração. Um manipulador de eventos pode então ser instalado que será notificado dessas alterações. Ele responde a transferência de dados de um objeto para outro. No entanto, quando há vários modos de exibição, também deve haver vários manipuladores de eventos e um monte de código é envolvido.

## <a name="the-data-binding-solution"></a>A solução de associação de dados

Vinculação de dados automatiza esse trabalho e renderiza os manipuladores de eventos desnecessários. (Os eventos são ainda necessários, no entanto, como a infra-estrutura de ligação de dados usa-los.) Associações de dados podem ser implementadas no código ou em XAML, mas eles são muito mais comuns em XAML em que eles ajudam a reduzir o tamanho do arquivo code-behind. Substituindo o código procedural em manipuladores de eventos por declarativas de código ou marcação, o aplicativo é simplificado e esclarecido.

Um dos dois objetos envolvidos em uma associação de dados quase sempre é um elemento que é derivada de `View` e faz parte da interface visual de uma página. O outro objeto é:

- Outro `View` derivados, geralmente na mesma página.
- Um objeto em um arquivo de código.

Em programas de demonstração, como aqueles na [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) de exemplo, associações de dados entre dois `View` derivados geralmente são mostrados para fins de clareza e simplicidade. No entanto, os mesmos princípios podem ser aplicados às associações de dados entre um `View` e outros objetos. Quando um aplicativo é compilado usando a arquitetura de Model-View-ViewModel (MVVM), a classe com dados subjacentes é frequentemente chamada de um ViewModel.

Associações de dados são exploradas na seguinte série de artigos:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Associações básicas](basic-bindings.md)

Aprenda a diferença entre a origem e destino de associação de dados e ver as associações de dados simples no código e XAML.

## <a name="binding-modebinding-modemd"></a>[Modo de associação](binding-mode.md)

Descubra como o modo de associação pode controlar o fluxo de dados entre os dois objetos.

## <a name="string-formattingstring-formattingmd"></a>[Formação de cadeia de caracteres](string-formatting.md)

Use uma associação de dados para formatar e exibir objetos como cadeias de caracteres.

## <a name="binding-pathbinding-pathmd"></a>[Caminho de associação](binding-path.md)

Aprofunde-se para o `Path` propriedade da ligação de dados para acessar subpropriedades e os membros da coleção.

## <a name="binding-value-convertersconvertersmd"></a>[Conversores de associação de valor](converters.md)

Use conversores de valor de associação para alterar os valores dentro de ligação de dados.

## <a name="binding-fallbacksbinding-fallbacksmd"></a>[Fallbacks de associação](binding-fallbacks.md)

Torna as associações de dados mais robusto definindo valores de fallback a ser usado se o processo de associação falhará.

## <a name="the-command-interfacecommandingmd"></a>[A interface de comando](commanding.md)

Implementar o `Command` propriedade com associações de dados.

## <a name="compiled-bindingscompiled-bindingsmd"></a>[Associações compiladas](compiled-bindings.md)

Use associações compiladas para melhorar o desempenho de associação de dados.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
