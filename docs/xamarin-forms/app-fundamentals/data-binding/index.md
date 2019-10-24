---
title: Associação de dados do Xamarin.Forms
description: A associação de dados é a técnica de vinculação de propriedades de dois objetos para que as alterações em uma propriedade sejam automaticamente refletidas na outra propriedade. A associação de dados é parte integrante da arquitetura do aplicativo MVVM (Model-View-ViewModel).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/27/2019
ms.openlocfilehash: fa92409b33717e528c3cfb83a24148c698836594
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697137"
---
# <a name="xamarinforms-data-binding"></a>Associação de dados do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_A vinculação de dados é a técnica de vincular propriedades de dois objetos para que as alterações em uma propriedade sejam refletidas automaticamente na outra propriedade. A vinculação de dados é parte integrante da arquitetura do aplicativo MVVM (Model-View-ViewModel)._

## <a name="the-data-linking-problem"></a>O problema de associação de dados

Um aplicativo Xamarin.Forms consiste em uma ou mais páginas, cada uma das quais geralmente contém vários objetos da interface do usuário chamados de *exibições*. Uma das principais tarefas do programa é manter essas exibições sincronizadas e controlar os diversos valores ou seleções que elas representam. Muitas vezes as exibições representam valores de uma fonte de dados subjacente, e o usuário manipula essas exibições para alterar esses dados. Quando a exibição é alterada, os dados subjacentes devem refletir essa alteração e, da mesma forma, quando os dados subjacentes são alterados, essa alteração deve ser refletida na exibição.

Para que esse trabalho seja realizado com êxito, o programa deve ser notificado das alterações nessas exibições ou nos dados subjacentes. A solução comum é definir eventos que sinalizam quando uma alteração ocorre. É possível então instalar um manipulador de eventos que é notificado sobre essas alterações. Ele responde transferindo dados de um objeto para outro. No entanto, quando há muitas exibições, também é preciso que haja vários manipuladores de eventos, e há muito código envolvido.

## <a name="the-data-binding-solution"></a>A solução de associação de dados

A associação de dados automatiza esse trabalho e torna os manipuladores de eventos desnecessários. (No entanto, os eventos ainda são necessários, pois a infraestrutura de vinculação de dados os usa.) As associações de dados podem ser implementadas no código ou em XAML, mas são muito mais comuns em XAML, em que ajudam a reduzir o tamanho do arquivo code-behind. Substituindo o código procedural em manipuladores de eventos por marcação ou código declarativo, o aplicativo é simplificado e esclarecido.

Um dos dois objetos envolvidos em uma associação de dados quase sempre é um elemento derivado de `View` e que faz parte da interface visual de uma página. O outro objeto é uma das seguintes opções:

- Outro derivado de `View`, geralmente na mesma página.
- Um objeto em um arquivo de código.

Em programas de demonstração, tais como aqueles na amostra [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos), associações de dados entre dois derivados de `View` geralmente são mostrados para fins de clareza e simplicidade. No entanto, os mesmos princípios podem ser aplicados às associações de dados entre um `View` e outros objetos. Quando um aplicativo é criado usando a arquitetura MVVM (Model-View-ViewModel), a classe com dados subjacentes geralmente é chamada de ViewModel.

Associações de dados são exploradas na seguinte série de artigos:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Associações básicas](basic-bindings.md)

Aprenda a diferença entre a origem e o destino da associação de dados e veja associações de dados simples em código e em XAML.

## <a name="binding-modebinding-modemd"></a>[Modo de associação](binding-mode.md)

Descubra como o modo de associação pode controlar o fluxo de dados entre os dois objetos.

## <a name="string-formattingstring-formattingmd"></a>[Formação de cadeia de caracteres](string-formatting.md)

Use uma associação de dados para formatar e exibir objetos como cadeias de caracteres.

## <a name="binding-pathbinding-pathmd"></a>[Caminho de associação](binding-path.md)

Aprofunde-se na propriedade `Path` da associação de dados para acessar subpropriedades e os membros da coleção.

## <a name="binding-value-convertersconvertersmd"></a>[Conversores de associação de valor](converters.md)

Use conversores de valor de associação para alterar os valores dentro da associação de dados.

## <a name="relative-bindingsrelative-bindingsmd"></a>[Associações relativas](relative-bindings.md)

Use associações relativas para definir a origem da associação em relação à posição do destino da associação.

## <a name="binding-fallbacksbinding-fallbacksmd"></a>[Fallbacks de associação](binding-fallbacks.md)

Torne as associações de dados mais robustas definindo valores de fallback que deverão ser usados se o processo de associação falhar.

## <a name="the-command-interfacecommandingmd"></a>[A interface de comando](commanding.md)

Implemente a propriedade `Command` com associações de dados.

## <a name="compiled-bindingscompiled-bindingsmd"></a>[Associações compiladas](compiled-bindings.md)

Use associações compiladas para melhorar o desempenho de associação de dados.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Capítulo de associação de dados do catálogo de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
