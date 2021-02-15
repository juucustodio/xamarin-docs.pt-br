---
title: Trabalhando com tabelas e células no Xamarin. iOS
description: Este documento contém links para vários guias que descrevem como exibir dados com o controle UITableView em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/06/2016
ms.openlocfilehash: fc2a76d97fe6c3bb52877bd58707872068f25968
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430814"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Trabalhando com tabelas e células no Xamarin. iOS

Esta seção apresenta as classes usadas para criar e exibir tabelas e, em seguida, fornece exemplos de como usá-las no Xamarin. iOS. Ele abordará o uso da aparência padrão para tabelas, personalizando o layout, implementando a edição e usando o Xamarin iOS designer para criar uma tabela visualmente. Às vezes, a exibição é obviamente uma lista de linhas (como o aplicativo de música) e outras vezes é difícil reconhecer o controle de tabela (como edição no aplicativo de contatos ou uma conversa no aplicativo de mensagens).

Para aqueles que trabalham em aplicativos de plataforma cruzada com o Xamarin. Android, o controle UITableView é semelhante à classe ListView no Android (e a classe UITableViewSource é semelhante às classes de adaptador do Android).

Esses artigos terão uma visão abrangente de como trabalhar com tabelas, incluindo:

- **Partes de tabela** – apresentando e explicando os elementos visuais do  `UITableView` controle. 
- **Exibindo dados em tabelas** – demonstrando como criar e preencher uma tabela, usar estilos de tabela e célula diferentes e evitar problemas de memória por meio da reciclagem de objetos de célula. 
- **Uso avançado** – Criando células personalizadas e usando os recursos de edição da classe UITableView. 
- **Criar uma tabela visualmente** – usando o Xamarin designer para IOS para criar uma interface orientada por tabela com um Storyboard. 

## <a name="contents"></a>Sumário

 [Funcionalidade de partes de tabela &amp;](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Como popular uma tabela usando dados](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Como personalizar a aparência de uma tabela](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Edição](~/ios/user-interface/controls/tables/editing.md)

 [Ações de linha](~/ios/user-interface/controls/tables/row-action.md)

 [Criando tabelas em um storyboard](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)

 [Como dimensionar automaticamente a altura da linha](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Links Relacionados

- [WorkingWithTables (exemplo)](/samples/xamarin/ios-samples/workingwithtables)
- [Tabelas em storyboards (exemplo)](/samples/xamarin/ios-samples/storyboardtable)
- [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md)
- [TableView receita de um storyboard](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introdução à caixa de diálogo MonoTouch.](~/ios/user-interface/monotouch.dialog/index.md)
- [Exemplo de TableEditing no github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Exemplo de TableParts no github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Exemplo de TableAndCellStyles no github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Referência de classe UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Referência de classe UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)