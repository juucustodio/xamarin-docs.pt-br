---
title: Funcionalidade e partes da tabela
ms.topic: article
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 1a47cad858e4b0b362da18ebe58142ade2574be2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="table-parts-and-functionality"></a>Funcionalidade e partes da tabela

Um UITableView pode ter um estilo 'simples' ou 'agrupado' e consiste nas seguintes partes:

-  [Cabeçalho de seção](#Section_Header)
-  [Células](#Cells) (ou linhas, se você preferir)
-  [Rodapé de página de seção](#Section_Footer)
-  [Índice](#Index)
-  [Modo de edição](#Edit_Features) (inclui 'passa o dedo para excluir' e arraste as alças para alterar a ordem de linha) 


Essas capturas de tela mostram como as linhas de seção, cabeçalhos, rodapés de páginas, controles de edição e o índice são exibidos.

 [ ![](table-parts-and-functionality-images/image1a.png "Essas capturas de tela mostram como as linhas de seção, cabeçalhos, rodapés de páginas, controles de edição e o índice são exibidos")](table-parts-and-functionality-images/image1a.png)

Essas partes são descritas em mais detalhes abaixo:

 <a name="Section_Header" />


## <a name="section-header"></a>Cabeçalho de seção

As células podem opcionalmente ser agrupadas em seções, rotuladas com um cabeçalho personalizado e/ou rotuladas com um rodapé. O cabeçalho pode ser definido com um valor de cadeia de caracteres ou uma exibição personalizada pode ser fornecida para permitir um layout diferente ou estilo.

 <a name="Cells" />


## <a name="cells"></a>Células

As células são o elemento de interface do usuário principal de uma tabela. Quando implementada corretamente, as células são reutilizadas para a eficiência da memória. Há quatro estilos de célula internas e você pode criar suas próprias células personalizadas – no código ou no Designer quando usando Storyboards.


## <a name="section-footer"></a>Rodapé de página de seção

O rodapé da seção opcional pode ser definido com um valor de cadeia de caracteres ou uma exibição personalizada pode ser fornecida para permitir um layout diferente ou estilo. Rodapés e cabeçalhos de seção podem ser definidas independentemente.

 <a name="Index" />


## <a name="index"></a>Índice

O índice é exibido como uma faixa de caracteres para a borda direita da tabela.
Tocar ou arrastando no índice acelera a rolagem para essa parte da tabela. Um índice é opcional, mas é recomendado para ajudar a navegar listas longas. Geralmente, um índice não é usado com o estilo agrupado.

 <a name="Edit_Features" />


## <a name="editing-mode"></a>Modo de edição

Alguns recursos de edição diferentes estão disponíveis:

-  Passe o dedo para excluir células individuais.
-  Modo de edição para revelar os botões de exclusão em cada linha 
-  Modo de edição para revelar os identificadores de ordenação novamente. 
-  Inserir novas células (com animação).


O restante deste documento mostra como implementar todos esses recursos UITableView com xamarin.

 <a name="Classes_Overview" />


## <a name="classes-overview"></a>Visão geral de classes

As classes primárias usadas para exibir os modos de exibição de tabela são mostradas aqui:

 [ ![](table-parts-and-functionality-images/classdiagram.png "As classes primárias usadas para exibir os modos de exibição de tabela são mostradas aqui")](table-parts-and-functionality-images/classdiagram.png)

A finalidade de cada classe é descrita abaixo:

-   **UITableView** – um modo de exibição que contém uma coleção de células dentro de um recipiente de rolagem. O modo de exibição de tabela geralmente usa toda a tela em um aplicativo de iPhone, mas pode existir como parte de uma exibição maior no iPad (ou aparecem em um popover). 
-   **UITableViewCell** – um modo de exibição que representa uma única célula (ou linha) em uma exibição de tabela. Há quatro tipos de célula interna e é possível criar células personalizadas em c# ou com o Designer do iOS. 
-   **UITableViewSource** – xamarin exclusivas classe abstrata que fornece todos os métodos necessários para exibir uma tabela, incluindo a contagem de linhas, retornando um modo de exibição de célula para cada linha, tratamento de seleção de linha e muitos outros recursos opcionais. Você *deve* subclasse esta opção para obter um trabalho UITableView. 
-   **NSIndexPath** – seção e a linha contém propriedades que identificam exclusivamente a posição de uma célula em uma tabela. 
-   **UITableViewController** – UIViewController uma pronto para uso com um UITableView codificado como seu modo de exibição e acessível por meio da propriedade do modo de tabela. 
-   **UIViewController** – se a tabela não ocupa a tela inteira, você pode adicionar um UITableView para qualquer UIViewController com seu quadro definida corretamente. 


UITableViewSource substitui as seguintes classes, que ainda estão disponíveis no xamarin, mas não são geralmente necessários:

-   **UITableViewDataSource** – Objective-C um protocolo que é modelado em xamarin como uma classe abstrata. Deve ser uma subclasse para fornecer uma tabela com um modo de exibição para cada célula, bem como informações sobre cabeçalhos, rodapés e o número de linhas e seções na tabela. 
-   **UITableViewDelegate** – Objective-C um protocolo que é modelado em xamarin como uma classe. Lida com a seleção de recursos e outros recursos de tabela opcionais de edição. 


Neste documento, os exemplos usam UITableViewSource em ignorar essas duas classes. Eles são mencionados aqui porque nenhum exemplo Objective-C encontrado na documentação da Apple fará referência-los, por isso é útil entender o que fazer (e que você pode usar UITableViewSource do xamarin).


## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
