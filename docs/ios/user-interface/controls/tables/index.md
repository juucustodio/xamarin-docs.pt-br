---
title: Trabalhar com tabelas e células no xamarin. IOS
description: Este documento leva a vários guias que descrevem como exibir dados com o controle de UITableView em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/06/2016
ms.openlocfilehash: 275c7553e465da67ca0780ea6aa9e986ca33b1f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379119"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Trabalhar com tabelas e células no xamarin. IOS

Esta seção apresenta as classes usadas para criar e exibir tabelas e fornece exemplos de como usá-los no xamarin. IOS. Ele abordará usando a aparência padrão para tabelas, personalizando o layout, a implementação de edição e usando o Designer do iOS Xamarin para criar uma tabela visualmente. Às vezes, a exibição é, obviamente, uma lista de linhas (por exemplo, o aplicativo de música) e outras vezes é difícil reconhecer o controle de tabela (como editar no aplicativo contatos ou uma conversa em um aplicativo de mensagens).

Para aqueles que trabalham em aplicativos de plataforma cruzada com xamarin. Android, o controle de UITableView é semelhante à classe ListView no Android (e a classe UITableViewSource é semelhante às classes de adaptador do Android).

Esses artigos levará um panorama abrangente trabalhando com tabelas, incluindo:

-   **Partes da tabela** – Introducing e explicando os elementos visuais do `UITableView` controle. 
-   **Exibindo dados em tabelas** – que demonstra como criar e popular uma tabela, use diferentes estilos de tabela e de célula e evitar problemas de memória por objetos cell de reciclagem. 
-   **Uso avançado** – criando células personalizadas e usando os recursos de edição da classe UITableView. 
-   **Criando uma tabela visualmente** – usando o Designer do Xamarin para iOS para criar uma interface orientada a tabela com um Storyboard. 

## <a name="contents"></a>Conteúdo

 [Tabela partes &amp; funcionalidade](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Preencher uma tabela com dados](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalizar a aparência de uma tabela](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Edição](~/ios/user-interface/controls/tables/editing.md)
 
 [Ações de linha](~/ios/user-interface/controls/tables/row-action.md)

 [Criando tabelas em um Storyboard](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Dimensionamento automático da altura da linha](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (amostra)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Tabelas em Storyboards (amostra)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [Storyboard uma receita de modo de tabela](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introdução ao MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Exemplo de TableEditing no Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Exemplo de TableParts no Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Exemplo de TableAndCellStyles no Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Referência de classe de UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Referência de classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
