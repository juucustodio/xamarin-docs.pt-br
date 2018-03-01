---
title: "Trabalhando com tabelas e células"
description: Exibindo dados usando UITableView com xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: f6abcaa3a771954785df83e80c7e46dd200e6986
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-tables-and-cells"></a>Trabalhando com tabelas e células


Esta seção apresenta as classes usadas para criar e exibir tabelas e fornece exemplos de como usá-los em xamarin. Ele aborda usando a aparência padrão para tabelas, personalizando o layout, Implementando edição e usando o Designer do iOS Xamarin para criar uma tabela visualmente. Às vezes, a exibição, obviamente, é uma lista de linhas (como o aplicativo de música) e outras vezes é difícil reconhecer o controle de tabela (como editar no aplicativo contatos ou uma conversa em aplicativos de mensagens).

Para aqueles que trabalham em aplicativos de plataforma cruzada com xamarin, o controle UITableView é semelhante à classe ListView no Android (e a classe UITableViewSource é similar a classes de adaptador do Android).

Esses artigos terão uma visão completa de trabalhar com tabelas, incluindo:

-   **Tabela partes** – Introdução e explica os elementos visuais do `UITableView` controle. 
-   **Exibindo dados em tabelas** – demonstra como criar e popular uma tabela, use diferentes estilos de tabela e de célula e evitar problemas de memória por objetos de célula de reciclagem. 
-   **O uso avançado** – criação personalizadas células e usando os recursos de edição da classe UITableView. 
-   **Criar uma tabela visualmente** – usando o Designer de Xamarin para iOS para criar uma interface orientada a tabela com um Storyboard. 


## <a name="contents"></a>Conteúdo

 [Tabela partes &amp; funcionalidade](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Populando uma tabela com dados](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalizando a aparência de uma tabela](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Edição](~/ios/user-interface/controls/tables/editing.md)
 
 [Ações de linha](~/ios/user-interface/controls/tables/row-action.md)

 [Criando tabelas em um Storyboard](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Altura da linha de dimensionamento automático](~/ios/user-interface/controls/tables/autosizing-row-height.md)


## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Tabelas no Storyboards (exemplo)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [Storyboard receita um modo de tabela](https://developer.xamarin.com/recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introdução ao MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Exemplo de TableEditing no Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Exemplo de TableParts no Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Exemplo de TableAndCellStyles no Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Referência de classe UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Referência de classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
