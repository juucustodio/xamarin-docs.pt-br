---
title: Associação de dados do xamarin. Forms
description: Associação de dados é a técnica de vincular as propriedades de dois objetos para que as alterações em uma propriedade são refletidas automaticamente na outra propriedade. Associação de dados é uma parte integrante da arquitetura do aplicativo Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a5ea5dcb5b108da52634f131fd36a91ba82f7da4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240347"
---
# <a name="xamarinforms-data-binding"></a>Associação de dados do xamarin. Forms

_Associação de dados é a técnica de vincular as propriedades de dois objetos para que as alterações em uma propriedade são refletidas automaticamente na outra propriedade. Associação de dados é uma parte integrante da arquitetura do aplicativo Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>O problema de vinculação de dados

Um aplicativo xamarin. Forms consiste em uma ou mais páginas, cada uma delas geralmente contém vários objetos de interface de usuário chamados *exibições*. Uma das principais tarefas do programa é para manter essas exibições sincronizadas e para controlar os vários valores ou seleções que eles representam. Geralmente os modos de exibição representam valores de uma fonte de dados subjacente, e o usuário manipula esses modos de exibição para alterar dados. Quando o modo de exibição for alterada, os dados subjacentes devem refletir essa alteração, e da mesma forma, quando os dados subjacentes são alterados, essa alteração deve ser refletida no modo de exibição.

Para lidar com esse trabalho com êxito, o programa deve ser notificado sobre alterações nessas exibições ou os dados subjacentes. A solução comum é definir eventos quando ocorre uma alteração de sinal. Um manipulador de eventos pode ser instalado que será notificado dessas alterações. Ele responde ao transferir dados de um objeto para outro. No entanto, quando há vários modos de exibição, também deve haver vários manipuladores de eventos e muito código obtém envolvido.

## <a name="the-data-binding-solution"></a>A solução de associação de dados

Associação de dados automatiza esse trabalho e renderiza os manipuladores de eventos desnecessários. (Os eventos são ainda é necessários, no entanto, como a infraestrutura de associação de dados usa-las.) Associações de dados podem ser implementadas em código ou em XAML, mas eles são muito mais comuns em XAML onde elas ajudam a reduzir o tamanho do arquivo code-behind. Substituindo o código de procedimento em manipuladores de eventos por código declarativo ou marcação, o aplicativo é simplificado e esclarecido.

Um dos dois objetos envolvidos em uma associação de dados é quase sempre um elemento que é derivada de `View` e faz parte da interface visual de uma página. O outro objeto é:

- Outro `View` derivados, normalmente na mesma página.
- Um objeto em um arquivo de código.

Em programas de demonstração, como aqueles no [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) de exemplo, associações de dados entre dois `View` derivados geralmente são mostrados para fins de clareza e simplicidade. No entanto, os mesmos princípios podem ser aplicados às associações de dados entre um `View` e outros objetos. Quando um aplicativo é compilado usando a arquitetura de Model-View-ViewModel (MVVM), a classe com dados subjacentes é geralmente chamada de um ViewModel.

Associações de dados são exploradas na seguinte série de artigos:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Associações básicas](basic-bindings.md)

Saber a diferença entre a associação de dados de origem e destino e consulte associações de dados simples em código e XAML.

## <a name="binding-modebinding-modemd"></a>[Modo de associação](binding-mode.md)

Descubra como o modo de associação pode controlar o fluxo de dados entre os dois objetos.

## <a name="string-formattingstring-formattingmd"></a>[Formação de cadeia de caracteres](string-formatting.md)

Use uma associação de dados para formatar e exibir objetos como cadeias de caracteres.

## <a name="binding-pathbinding-pathmd"></a>[Caminho de associação](binding-path.md)

Mergulhar no `Path` propriedade da associação de dados para acessar subpropriedades e membros da coleção.

## <a name="binding-value-convertersconvertersmd"></a>[Conversores de associação de valor](converters.md)

Use conversores de valor de associação para alterar os valores de associação de dados.

## <a name="the-command-interfacecommandingmd"></a>[A interface de comando](commanding.md)

Implementar o `Command` propriedade com associações de dados.



## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
