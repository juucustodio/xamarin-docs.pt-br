---
title: Xamarin.Forms Associação de dados
description: A associação de dados é a técnica de vinculação de propriedades de dois objetos para que as alterações em uma propriedade sejam automaticamente refletidas na outra propriedade. A associação de dados é parte integrante da arquitetura do aplicativo MVVM (Model-View-ViewModel).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 819e139c412d08f37aee28251f37a86d341487de
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374570"
---
# <a name="no-locxamarinforms-data-binding"></a>Xamarin.Forms Associação de dados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/databindingdemos)

_A vinculação de dados é a técnica de vincular propriedades de dois objetos para que as alterações em uma propriedade sejam refletidas automaticamente na outra propriedade. A vinculação de dados é parte integrante da arquitetura do aplicativo MVVM (Model-View-ViewModel)._

## <a name="the-data-linking-problem"></a>O problema de associação de dados

Um Xamarin.Forms aplicativo consiste em uma ou mais páginas, cada uma delas geralmente contém vários objetos de interface do usuário chamados *exibições*. Uma das principais tarefas do programa é manter essas exibições sincronizadas e controlar os diversos valores ou seleções que elas representam. Muitas vezes as exibições representam valores de uma fonte de dados subjacente, e o usuário manipula essas exibições para alterar esses dados. Quando a exibição é alterada, os dados subjacentes devem refletir essa alteração e, da mesma forma, quando os dados subjacentes são alterados, essa alteração deve ser refletida na exibição.

Para que esse trabalho seja realizado com êxito, o programa deve ser notificado das alterações nessas exibições ou nos dados subjacentes. A solução comum é definir eventos que sinalizam quando uma alteração ocorre. É possível então instalar um manipulador de eventos que é notificado sobre essas alterações. Ele responde transferindo dados de um objeto para outro. No entanto, quando há muitas exibições, também é preciso que haja vários manipuladores de eventos, e há muito código envolvido.

## <a name="the-data-binding-solution"></a>A solução de associação de dados

A associação de dados automatiza esse trabalho e torna os manipuladores de eventos desnecessários. Associações de dados podem ser implementadas no código ou em XAML, mas são muito mais comuns em XAML, em que elas ajudam a reduzir o tamanho do arquivo code-behind. Substituindo o código procedural em manipuladores de eventos por marcação ou código declarativo, o aplicativo é simplificado e esclarecido.

Um dos dois objetos envolvidos em uma associação de dados quase sempre é um elemento derivado de `View` e que faz parte da interface visual de uma página. O outro objeto é uma das seguintes opções:

- Outro derivado de `View`, geralmente na mesma página.
- Um objeto em um arquivo de código.

Em programas de demonstração, tais como aqueles na amostra [**DataBindingDemos**](/samples/xamarin/xamarin-forms-samples/databindingdemos), associações de dados entre dois derivados de `View` geralmente são mostrados para fins de clareza e simplicidade. No entanto, os mesmos princípios podem ser aplicados às associações de dados entre um `View` e outros objetos. Quando um aplicativo é criado usando a arquitetura MVVM (Model-View-ViewModel), a classe com dados subjacentes geralmente é chamada de ViewModel.

Associações de dados são exploradas na seguinte série de artigos:

## <a name="basic-bindings"></a>[Associações básicas](basic-bindings.md)

Aprenda a diferença entre a origem e o destino da associação de dados e veja associações de dados simples em código e em XAML.

## <a name="binding-mode"></a>[Modo de associação](binding-mode.md)

Descubra como o modo de associação pode controlar o fluxo de dados entre os dois objetos.

## <a name="string-formatting"></a>[Formação de cadeia de caracteres](string-formatting.md)

Use uma associação de dados para formatar e exibir objetos como cadeias de caracteres.

## <a name="binding-path"></a>[Caminho de associação](binding-path.md)

Aprofunde-se na propriedade `Path` da associação de dados para acessar subpropriedades e os membros da coleção.

## <a name="binding-value-converters"></a>[Associação de conversores de valor](converters.md)

Use conversores de valor de associação para alterar os valores dentro da associação de dados.

## <a name="relative-bindings"></a>[Associações relativas](relative-bindings.md)

Use associações relativas para definir a origem da associação em relação à posição do destino da associação.

## <a name="binding-fallbacks"></a>[Associação de fallbacks](binding-fallbacks.md)

Torne as associações de dados mais robustas definindo valores de fallback que deverão ser usados se o processo de associação falhar.

## <a name="multi-bindings"></a>[Associações múltiplas](multibinding.md)

Anexe uma coleção de [`Binding`](xref:Xamarin.Forms.Binding) objetos a uma única propriedade de destino de associação.

## <a name="the-command-interface"></a>[A interface de comando](commanding.md)

Implemente a propriedade `Command` com associações de dados.

## <a name="compiled-bindings"></a>[Associações compiladas](compiled-bindings.md)

Use associações compiladas para melhorar o desempenho de associação de dados.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Capítulo de vinculação de dados do Xamarin.Forms livro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensões de marcação do XAML](~/xamarin-forms/xaml/markup-extensions/index.md)