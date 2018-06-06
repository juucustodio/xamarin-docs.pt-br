---
title: Trabalhando com ações de linha no xamarin
description: Este guia demonstra como criar ações personalizadas passe o dedo para linhas de tabela com UISwipeActionsConfiguration ou UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: 4be8b6dc66c9c047e6662067e7e3ecf81ab22893
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789935"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Trabalhando com ações de linha no xamarin

_Este guia demonstra como criar ações personalizadas passe o dedo para linhas de tabela com UISwipeActionsConfiguration ou UITableViewRowAction_

![Demonstração de ações de passe o dedo em linhas](row-action-images/action02.png)

iOS fornece duas maneiras de executar ações em uma tabela: `UISwipeActionsConfiguration` e `UITableViewRowAction`.

`UISwipeActionsConfiguration` foi introduzido no iOS 11 e é usado para definir um conjunto de ações que devem levar colocar quando swipes o usuário _em qualquer direção_ em uma linha em uma exibição de tabela. Esse comportamento é semelhante ao que o mail. App nativo 

O `UITableViewRowAction` classe é usada para definir uma ação que ocorrerá quando os swipes usuário deixado horizontalmente em uma linha em uma exibição de tabela.
Por exemplo, ao editar uma tabela, passar o dedo esquerda em uma linha exibe um **excluir** botão por padrão. Anexando a várias instâncias do `UITableViewRowAction` de classe para um `UITableView`, várias ações personalizadas podem ser definidas, cada um com seu próprio texto, formatação e comportamento.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Há um três etapas necessárias para implementar passe o dedo para ações com `UISwipeActionsConfiguration`:

1. Substituir `GetLeadingSwipeActionsConfiguration` e/ou `GetTrailingSwipeActionsConfiguration` métodos. Esses métodos retornam um `UISwipeActionsConfiguration`. 
2. Criar uma instância de `UISwipeActionsConfiguration` a ser retornado. Essa classe usa uma matriz de `UIContextualAction`.
3. Criará um `UIContextualAction`.

Essas são explicadas em mais detalhes nas seções a seguir.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implementando os métodos SwipeActionsConfigurations

`UITableViewController` (e também `UITableViewSource` e `UITableViewDelegate`) contêm dois métodos: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration`, que são usadas para implementar um conjunto de ações de passar em uma linha do modo de exibição de tabela. A ação de passe o dedo para a esquerda se refere a um dedo do lado esquerdo da tela em um idioma da esquerda para a direita e do lado direito da tela em um idioma da direita para esquerda. 

O exemplo a seguir (da [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) exemplo) demonstra a implementação da configuração de passe o dedo para a esquerda. Duas ações são criadas a partir das ações contextuais, que são explicadas [abaixo](#create-uicontextualaction). Essas ações, em seguida, são passadas para um recentemente inicializado [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), que é usado como o valor de retorno.


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. Criar uma instância de um `UISwipeActionsConfiguration`

Criar uma instância de um `UISwipeActionsConfiguration` usando o `FromActions` método para adicionar uma nova matriz de `UIContextualAction`s, conforme mostrado no trecho de código a seguir:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

É importante observar que a ordem na qual exibem suas ações depende de como eles são passados para a matriz. Por exemplo, o código acima para swipes à esquerda exibe as ações como assim:

![à esquerda passe o dedo para ações exibido em uma linha de tabela](row-action-images/action03.png)

As ações para a direita swipes, serão exibidas conforme ilustrado na imagem a seguir:

![à direita passe o dedo para ações exibido em uma linha de tabela](row-action-images/action04.png)

Este trecho de código também usa o novo `PerformsFirstActionWithFullSwipe` propriedade. Por padrão, essa propriedade é definida como true, o que significa que a primeira ação na matriz acontece quando um usuário totalmente passa em uma linha. Se você tiver uma ação que não é destrutiva (por exemplo "excluir", isso pode não ser ideal comportamento e, portanto, você deve configurá-lo `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Criar um `UIContextualAction`

A ação contextual é onde você realmente criar a ação que será exibida quando o usuário passa de uma linha da tabela.

Para inicializar uma ação que você deve fornecer um `UIContextualActionStyle`, um título e um `UIContextualActionHandler`. O `UIContextualActionHandler` usa três parâmetros: uma ação, a ação exibida na exibição e um processador de conclusão:

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Várias propriedades visuais, como a cor de plano de fundo ou imagem da ação podem ser editadas. O trecho de código acima demonstra a adição de uma imagem para a ação e definir sua cor de fundo azul.

Depois que as ações contextuais foram criadas, eles podem usar para inicializar o `UISwipeActionsConfiguration` no `GetLeadingSwipeActionsConfiguration` método.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Para definir uma ou mais ações de linha personalizado para um `UITableView`, você precisará criar uma instância do `UITableViewDelegate` classe e substituir o `EditActionsForRow` método. Por exemplo:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

Estático `UITableViewRowAction.Create` método é usado para criar um novo `UITableViewRowAction` que exibirá um **Hi** botão quando swipes o usuário ficaram horizontalmente em uma linha na tabela. Mais tarde uma nova instância do `TableDelegate` é criado e anexado para o `UITableView`. Por exemplo:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando o código acima é executado e swipes o usuário à esquerda em uma linha de tabela, o **Hi** botão será exibido em vez do **excluir** botão é exibido por padrão:

[![](row-action-images/action01.png "Botão Hi sendo exibido em vez de no botão Excluir")](row-action-images/action01.png#lightbox)

Se o usuário toca o **Hi** botão `Hello World!` serão gravados para o console no Visual Studio para Mac ou o Visual Studio quando o aplicativo é executado no modo de depuração.



## <a name="related-links"></a>Links relacionados

- [TableSwipeActions (exemplo)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
