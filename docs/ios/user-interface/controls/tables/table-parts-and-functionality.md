---
title: Partes da tabela e a funcionalidade no xamarin. IOS
description: Este documento descreve as várias partes de um UITableView no iOS. Ele aborda os cabeçalhos de seção, células, rodapés de seção, o índice e modo de edição.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c4d788cce12a9aabdd1170cd1a52915f3b30285f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113944"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Partes da tabela e a funcionalidade no xamarin. IOS

Um UITableView pode ter um estilo 'simples' ou 'agrupado' e consiste nas seguintes partes:

-  [Cabeçalho de seção](#Section_Header)
-  [Células](#Cells) (ou linhas, se você preferir)
-  [Seção de rodapé](#Section_Footer)
-  [Índice](#Index)
-  [Modo de edição](#Edit_Features) (inclui 'passe o dedo para excluir' e arraste as alças para alterar a ordem de linha) 

Essas capturas de tela mostram como as linhas de seção, cabeçalhos, rodapés de páginas, controles de edição e o índice são exibidos.

 [![](table-parts-and-functionality-images/image1a.png "Essas capturas de tela mostram como as linhas de seção, cabeçalhos, rodapés de páginas, controles de edição e o índice são exibidos")](table-parts-and-functionality-images/image1a.png#lightbox)

Essas partes são descritas em mais detalhes abaixo:

<a name="Section_Header" />

## <a name="section-header"></a>Cabeçalho de seção

Células, opcionalmente, podem ser agrupadas em seções, rotuladas com um cabeçalho personalizado e rotuladas com um rodapé. O cabeçalho pode ser definido com um valor de cadeia de caracteres ou uma exibição personalizada pode ser fornecida para permitir um layout diferente ou estilo.

<a name="Cells" />

## <a name="cells"></a>Células

As células são o elemento de interface do usuário principal para uma tabela. Quando implementada corretamente, as células são reutilizadas para maior eficiência de memória. Há quatro estilos de célula internos e você pode criar suas próprias células personalizadas – no código ou no Designer ao usar Storyboards.

<a name="Section_Footer"/>

## <a name="section-footer"></a>Seção de rodapé

O rodapé da seção opcional pode ser definido com um valor de cadeia de caracteres ou uma exibição personalizada pode ser fornecida para permitir um layout diferente ou estilo. Rodapés e cabeçalhos de seção podem ser definidas independentemente.

<a name="Index" />

## <a name="index"></a>Índice

O índice é exibido como uma faixa de caracteres para baixo a borda direita da tabela.
Tocar ou arrastando o índice acelera a rolagem para essa parte da tabela. Um índice é opcional, mas é recomendável para ajudar a navegar listas longas. Geralmente, um índice não é usado com o estilo de agrupado.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Modo de edição

Há alguns recursos de edição diferentes disponíveis:

- Passe o dedo para excluir células individuais.
- Modo de edição para revelar os botões de exclusão em cada linha 
- Modo de edição para revelar as alças de ordenação novamente. 
- Inserindo novas células (com a animação).

O restante deste documento mostra como implementar todos esses recursos de UITableView com xamarin. IOS.


## <a name="classes-overview"></a>Visão geral de classes

Classes principais usadas para exibir os modos de exibição de tabela são mostradas aqui:

[![](table-parts-and-functionality-images/classdiagram.png "Classes principais usadas para exibir os modos de exibição de tabela são mostradas aqui")](table-parts-and-functionality-images/classdiagram.png#lightbox)

A finalidade de cada classe é descrita abaixo:

- **UITableView** – uma exibição que contém uma coleção de células dentro de um contêiner de rolagem. O modo de exibição de tabela normalmente usa a tela inteira em um aplicativo de iPhone, mas podem existir como parte de uma exibição maior no iPad (ou aparecer em um pop-over). 
- **UITableViewCell** – um modo de exibição que representa uma única célula (ou linha) em uma exibição de tabela. Há quatro tipos de célula interna e é possível criar personalizadas células ambos os C# ou com o Designer do iOS. 
- **UITableViewSource** – xamarin. IOS exclusivas classe abstrata que fornece todos os métodos necessários para exibir uma tabela, incluindo a contagem de linhas, retornando uma exibição de célula para cada linha, tratamento de seleção de linha e muitos outros recursos opcionais. Você *deve* subclasse esta opção para obter um trabalho de UITableView. 
- **NSIndexPath** – seção e a linha contém propriedades que identificam exclusivamente a posição de uma célula em uma tabela. 
- **UITableViewController** – um UIViewController prontos para uso com a UITableView codificada como seu modo de exibição e acessível por meio da propriedade do modo de tabela. 
- **UIViewController** – se a tabela não ocupa a tela inteira, você pode adicionar um UITableView para qualquer UIViewController com seu quadro definida corretamente. 

UITableViewSource substitui as duas classes a seguir, que ainda estão disponíveis no xamarin. IOS, mas não são geralmente necessários:

- **UITableViewDataSource** – Objective-C um protocolo que é modelado no xamarin. IOS como uma classe abstrata. Deve ser uma subclasse para fornecer uma tabela com um modo de exibição para cada célula, bem como informações sobre cabeçalhos, rodapés e o número de linhas e seções na tabela. 
- **UITableViewDelegate** – Objective-C um protocolo que é modelado no xamarin. IOS como uma classe. Lida com a seleção, edição de recursos e outros recursos de tabela opcionais. 

Neste documento, os exemplos usam UITableViewSource e ignorar essas duas classes. Eles são mencionados aqui porque nenhum exemplo de Objective-C encontrado na documentação da Apple fará referência a elas, portanto, é útil para entender o que fazer (e que você pode usar UITableViewSource do xamarin. IOS).

## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (amostra)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
