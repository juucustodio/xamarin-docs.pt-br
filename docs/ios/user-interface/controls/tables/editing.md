---
title: Edição de tabelas com xamarin. IOS
description: Este documento descreve como editar tabelas no xamarin. IOS. Ele aborda passe o dedo para excluir, de modo de edição e inserção de linha.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 1267de341a88130c18254f414d2fbb1c42595a0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61195876"
---
# <a name="editing-tables-with-xamarinios"></a>Edição de tabelas com xamarin. IOS

Recursos de edição de tabela são habilitados por meio da substituição de métodos em um `UITableViewSource` subclasse. O comportamento de edição mais simples é o gesto de passar o dedo para exclusão que pode ser implementado com um único método de substituição.
Edição mais complexa (incluindo mover linhas) pode ser feito com a tabela no modo de edição.

## <a name="swipe-to-delete"></a>Passe o dedo para exclusão

O passe o dedo para excluir o recurso é um gesto natural no iOS que os usuários esperam. 

 [![](editing-images/image10.png "Exemplo de passar o dedo para exclusão")](editing-images/image10.png#lightbox)

Há três substituições de método que afetam o gesto de passar o dedo para mostrar uma **excluir** botão em uma célula:

-   **CommitEditingStyle** – a fonte de tabela detecta se esse método é substituído e habilita automaticamente o gesto de passar o dedo para exclusão. A implementação do método deve chamar `DeleteRows` sobre o `UITableView` para fazer com que as células desapareça e também remover os dados subjacentes de seu modelo (por exemplo, uma matriz, dicionário ou os banco de dados). 
-   **CanEditRow** – CommitEditingStyle se for substituído, todas as linhas são consideradas editável. Se esse método é implementado e retorna false (algumas linhas específicas ou para todas as linhas), em seguida, o gesto de passar o dedo para exclusão não estará disponível na célula. 
-   **TitleForDeleteConfirmation** – opcionalmente, especifica o texto para o **excluir** botão. Se esse método não for implementado será o texto do botão "Excluir". 


Esses métodos são implementados no `TableSource` classe da seguinte maneira:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

Para este exemplo o `UITableViewSource` foi atualizado para usar um `List<TableItem>` (em vez de uma matriz de cadeia de caracteres) como a fonte de dados porque ele dá suporte à adição e exclusão de itens da coleção.


## <a name="edit-mode"></a>Modo de edição

Quando uma tabela está no modo de edição o usuário vê um widget de 'stop' vermelho em cada linha, que revela um botão Excluir quando tocado. A tabela também exibe um ícone de 'Identificador' para indicar que a linha pode ser arrastada para alterar a ordem.
O **TableEditMode** amostra implementa esses recursos, conforme mostrado.

 [![](editing-images/image11.png "O exemplo de TableEditMode implementa esses recursos, conforme mostrado")](editing-images/image11.png#lightbox)

Há vários métodos diferentes em `UITableViewSource` que afetam o comportamento do modo de edição de uma tabela:

-   **CanEditRow** – indica se cada linha podem ser editada. Retorne false para evitar a exclusão no modo de edição e passar o dedo para exclusão. 
-   **CanMoveRow** – retorno true para habilitar o 'alça de movimentação' ou false para impedir a movimentação. 
-   **EditingStyleForRow** – quando a tabela está em modo de edição, o valor de retorno desse método determina se a célula exibe o ícone de exclusão vermelho ou verde Adicionar ícone. Retornar `UITableViewCellEditingStyle.None` se a linha não deve ser editável. 
-   **MoveRow** – chamado quando uma linha é movida para que a estrutura de dados subjacente pode ser modificada de acordo com os dados como ele é exibido na tabela. 


A implementação para os três primeiros métodos é relativamente simples –, a menos que você deseja usar o `indexPath` para alterar o comportamento de linhas específicos, codificar apenas o valores de retorno para a tabela inteira.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

O `MoveRow` implementação é um pouco mais complicada porque ele precisa alterar a estrutura de dados subjacente para coincidir com a nova ordem. Como os dados são implementados como um `List` o código a seguir exclui o item de dados no seu local antigo e o insere no novo local. Se os dados foram armazenados em uma tabela de banco de dados SQLite com uma coluna de 'order' (por exemplo), esse método em vez disso, precisa executar algumas operações de SQL para reordenar os números na coluna.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Por fim, para obter a tabela em modo de edição, o **edite** botão precisa chamar `SetEditing` como este

```csharp
table.SetEditing (true, true);
```

e quando é concluído, o usuário de edição, o **feito** botão deve desativar modo de edição:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Estilo de edição de inserção de linha

Inserção de linha de dentro da tabela é uma interface de usuário incomum – o principal exemplo nos aplicativos do iOS padrão é o **Editar contato** tela. Esta captura de tela mostra como funciona a funcionalidade de inserção de linha – Editar modo há adicional de linha que (quando clicado) insere linhas adicionais nos dados. Quando a edição estiver concluída, temporários **(Adicionar nova)** linha será removida.

 [![](editing-images/image12.png "Quando a edição for concluída, temporárias adicionar nova linha será removida")](editing-images/image12.png#lightbox)

Há vários métodos diferentes em `UITableViewSource` que afetam o comportamento do modo de edição de uma tabela. Esses métodos foram implementados da seguinte maneira no código de exemplo:

-   **EditingStyleForRow** – retorna `UITableViewCellEditingStyle.Delete` para as linhas que contêm dados e retorna `UITableViewCellEditingStyle.Insert` para a última linha (que será adicionada especificamente para se comportar como um botão de inserção). 
-   **CustomizeMoveTarget** – enquanto o usuário está movendo uma célula, o valor de retorno desse método opcional pode substituir sua preferência de local. Isso significa que você pode impedir que eles 'Descartar' a célula em determinadas posições – como no exemplo que impede que qualquer linha que está sendo movida após o **(Adicionar nova)** linha. 
-   **CanMoveRow** – retorno true para habilitar o 'alça de movimentação' ou false para impedir a movimentação. No exemplo, a última linha tem a 'alça de movimentação' oculta porque ele destina-se ao servidor como um botão de inserção. 


Também adicionamos dois métodos personalizados para adicionar a linha de 'insert' e, em seguida, removê-lo novamente quando não for mais necessário. Eles são chamados da **edite** e **feito** botões:

-   **WillBeginTableEditing** – quando o **edite** botão é tocadas ele chamadas `SetEditing` para colocar a tabela no modo de edição. Isso dispara o método WillBeginTableEditing onde podemos exibir o **(Adicionar nova)** linha no final da tabela para atuar como um botão' insert'. 
-   **DidFinishTableEditing** – quando o usuário toca no botão concluído `SetEditing` é chamado novamente para desativar o modo de edição. O exemplo de código remove a **(Adicionar nova)** linha da tabela durante a edição não é mais necessária. 


Essas substituições de método são implementadas no arquivo de exemplo **TableEditModeAdd/Code/TableSource.cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Esses dois métodos personalizados são usados para adicionar e remover as **(Adicionar nova)** linha quando a tabela do modo de edição está habilitada ou desabilitada:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Por fim, esse código cria uma instância de **edite** e **feito** botões com lambdas que habilitar ou desabilitar o modo de edição quando eles são tocados:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Esse padrão de interface do usuário de inserção de linha não é usado com muita frequência, no entanto, você também pode usar o `UITableView.BeginUpdates` e `EndUpdates` métodos para animar a inserção ou remoção de células em qualquer tabela. A regra para usar esses métodos é que a diferença no valor retornado por `RowsInSection` entre o `BeginUpdates` e `EndUpdates` chamadas devem corresponder ao número líquido de células adicionada/excluída com a `InsertRows` e `DeleteRows` métodos. Se a fonte de dados subjacente não é alterado para corresponder as inserções/exclusões na exibição de tabela, que ocorrerá um erro.


## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (amostra)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
