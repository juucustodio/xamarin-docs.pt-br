---
title: Partes de tabela e funcionalidade no Xamarin. iOS
description: Este documento descreve as várias partes de um UITableView no iOS. Ele aborda cabeçalhos de seção, células, rodapés de seção, índice e modo de edição.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: edc5ec51499116fc2fabeb03327b32a689dc0c49
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279338"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Partes de tabela e funcionalidade no Xamarin. iOS

Um UITableView pode ter um estilo ' agrupado ' ou ' Plain ' e consiste nas seguintes partes:

- [Cabeçalho da seção](#Section_Header)
- [Células](#Cells) (ou linhas, se preferir)
- [Rodapé da seção](#Section_Footer)
- [Índice](#Index)
- [Modo de edição](#Edit_Features) (inclui "passar o dedo para excluir" e arrastar identificadores para alterar a ordem das linhas) 

Essas capturas de tela mostram como as linhas de seção, os cabeçalhos, os rodapés, os controles de edição e o índice são exibidos.

 [![](table-parts-and-functionality-images/image1a.png "Essas capturas de tela mostram como as linhas de seção, os cabeçalhos, os rodapés, os controles de edição e o índice são exibidos")](table-parts-and-functionality-images/image1a.png#lightbox)

Essas partes são descritas em mais detalhes abaixo:

<a name="Section_Header" />

## <a name="section-header"></a>Cabeçalho da seção

As células podem, opcionalmente, ser agrupadas em seções, rotuladas com um cabeçalho personalizado e/ou rotuladas com um rodapé. O cabeçalho pode ser definido com um valor de cadeia de caracteres ou um modo de exibição personalizado pode ser fornecido para permitir um layout ou estilo diferente.

<a name="Cells" />

## <a name="cells"></a>Células

As células são o elemento principal da interface do usuário para uma tabela. Quando implementadas corretamente, as células são reutilizadas para a eficiência da memória. Há quatro estilos de célula internos, e você pode criar suas próprias células personalizadas, seja no código, ou no designer ao usar storyboards.

<a name="Section_Footer"/>

## <a name="section-footer"></a>Rodapé da seção

O rodapé de seção opcional pode ser definido com um valor de cadeia de caracteres ou um modo de exibição personalizado pode ser fornecido para permitir um layout ou estilo diferente. Cabeçalhos e rodapés de seção podem ser definidos de forma independente.

<a name="Index" />

## <a name="index"></a>Índice

O índice aparece como uma faixa de caracteres na borda direita da tabela.
Tocar ou arrastar no índice acelera a rolagem para essa parte da tabela. Um índice é opcional, mas é recomendado para ajudar a navegar por listas longas. Um índice geralmente não é usado com o estilo agrupado.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Modo de edição

Há alguns recursos de edição diferentes disponíveis:

- Passe o dedo para excluir células individuais.
- Entrando no modo de edição para revelar os botões de exclusão em cada linha 
- Entrando no modo de edição para revelar os identificadores de reordenação. 
- Inserindo novas células (com animação).

O restante deste documento mostra como implementar todos esses recursos do UITableView com o Xamarin. iOS.


## <a name="classes-overview"></a>Visão geral de classes

As classes primárias usadas para exibir exibições de tabela são mostradas aqui:

[![](table-parts-and-functionality-images/classdiagram.png "As classes primárias usadas para exibir exibições de tabela são mostradas aqui")](table-parts-and-functionality-images/classdiagram.png#lightbox)

A finalidade de cada classe é descrita abaixo:

- **UITableView** – uma exibição que contém uma coleção de células dentro de um contêiner de rolagem. A exibição de tabela normalmente usa a tela inteira em um aplicativo do iPhone, mas pode existir como parte de uma exibição maior no iPad (ou aparecer em um popover). 
- **UITableViewCell** – uma exibição que representa uma única célula (ou linha) em uma exibição de tabela. Há quatro tipos de célula internos e é possível criar células personalizadas no ou com o designer do C# Ios. 
- **UITableViewSource** – Xamarin. Ios-classe abstrata exclusiva que fornece todos os métodos necessários para exibir uma tabela, incluindo a contagem de linhas, retornando uma exibição de célula para cada linha, manipulando a seleção de linhas e muitos outros recursos opcionais. Você *deve* fazer uma subclasse para obter um UITableView funcionando. 
- **NSIndexPath** – contém propriedades de linha e seção que identificam exclusivamente a posição de uma célula em uma tabela. 
- **UITableViewController** – uma UIViewController pronta para uso que tem um UITableView codificado como sua exibição e acessível por meio da propriedade Tableview. 
- **UIViewController** – se a tabela não ocupar a tela inteira, você poderá adicionar um UITableView a qualquer UIViewController com seu conjunto de quadros adequadamente. 

O UITableViewSource substitui as duas classes a seguir, que ainda estão disponíveis no Xamarin. iOS, mas que normalmente não são necessárias:

- **UITableViewDataSource** – um protocolo Objective-C que é modelado no Xamarin. Ios como uma classe abstrata. Deve ser subclasse para fornecer uma tabela com uma exibição para cada célula, bem como informações sobre cabeçalhos, rodapés e o número de linhas e seções na tabela. 
- **UITableViewDelegate** – um protocolo Objective-C que é modelado no Xamarin. Ios como uma classe. Lida com seleção, recursos de edição e outros recursos de tabela opcionais. 

Neste documento, todos os exemplos usam UITableViewSource e ignoram essas duas classes. Eles são mencionados aqui porque qualquer exemplo de Objective-C encontrado na documentação da Apple fará referência a eles, portanto, é útil entender o que eles fazem (e que você pode usar o UITableViewSource do Xamarin. iOS em vez disso).

## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
