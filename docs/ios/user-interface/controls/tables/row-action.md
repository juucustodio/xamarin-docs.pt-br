---
title: Trabalhando com ações de linha no Xamarin. iOS
description: Este guia demonstra como criar ações de passar o dedo personalizadas para linhas de tabela com UISwipeActionsConfiguration ou UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 2cb453996a43d1e70f4fb818c86f6215c213b988
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429746"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Trabalhando com ações de linha no Xamarin. iOS

_Este guia demonstra como criar ações de passar o dedo personalizadas para linhas de tabela com UISwipeActionsConfiguration ou UITableViewRowAction_

![Demonstrando ações de passar o dedo em linhas](row-action-images/action02.png)

o iOS fornece duas maneiras de executar ações em uma tabela: `UISwipeActionsConfiguration` e `UITableViewRowAction` .

`UISwipeActionsConfiguration` foi introduzido no iOS 11 e é usado para definir um conjunto de ações que devem ocorrer quando o usuário passa _em qualquer direção_ em uma linha em uma exibição de tabela. Esse comportamento é semelhante ao do Native mail. app

A `UITableViewRowAction` classe é usada para definir uma ação que ocorrerá quando o usuário passar horizontalmente para a esquerda em uma linha em uma exibição de tabela.
Por exemplo, ao editar uma tabela, o passar o dedo para a esquerda em uma linha exibe um botão **excluir** por padrão. Ao anexar várias instâncias da `UITableViewRowAction` classe a uma `UITableView` , várias ações personalizadas podem ser definidas, cada uma com seu próprio texto, formatação e comportamento.

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Há três etapas necessárias para implementar ações de passar o dedo com `UISwipeActionsConfiguration` :

1. Substituir `GetLeadingSwipeActionsConfiguration` e/ou `GetTrailingSwipeActionsConfiguration` métodos. Esses métodos retornam um `UISwipeActionsConfiguration` .
2. Instanciar o `UISwipeActionsConfiguration` a ser retornado. Essa classe usa uma matriz de `UIContextualAction` .
3. Criará um `UIContextualAction`.

Eles são explicados com mais detalhes nas seções a seguir.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. implementando os métodos SwipeActionsConfigurations

`UITableViewController` (e também `UITableViewSource` e `UITableViewDelegate` ) contêm dois métodos: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration` , que são usados para implementar um conjunto de ações de passar o dedo em uma linha de exibição de tabela. A ação de passar o dedo à esquerda refere-se a um dedo do lado esquerdo da tela em um idioma da esquerda para a direita e do lado direito da tela em um idioma da direita para a esquerda.

O exemplo a seguir (do exemplo [TableSwipeActions](/samples/xamarin/ios-samples/tableswipeactions) ) demonstra a implementação da configuração do dedo à esquerda. Duas ações são criadas a partir das ações contextuais, que são explicadas [abaixo](#create-uicontextualaction). Essas ações são passadas para uma inicializada recentemente [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations) , que é usada como o valor de retorno.

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

<a name="create-uiswipeactionsconfigurations"></a>

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. instanciar um `UISwipeActionsConfiguration`

Instancie um `UISwipeActionsConfiguration` usando o `FromActions` método para adicionar uma nova matriz de `UIContextualAction` s, conforme mostrado no seguinte trecho de código:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

É importante observar que a ordem na qual suas ações são exibidas depende de como elas são passadas para sua matriz. Por exemplo, o código acima para os principais toques exibe as ações da seguinte forma:

![Ações de deslizamento à esquerda exibidas em uma linha de tabela](row-action-images/action03.png)

Para os deslizamentos à direita, as ações serão exibidas conforme ilustrado na imagem a seguir:

![Ações de passar o dedo à direita exibidas em uma linha da tabela](row-action-images/action04.png)

Esse trecho de código também usa a nova `PerformsFirstActionWithFullSwipe` propriedade. Por padrão, essa propriedade é definida como true, o que significa que a primeira ação na matriz ocorrerá quando um usuário passar completamente em uma linha. Se você tiver uma ação que não é destrutiva (por exemplo, "excluir", isso pode não ser o comportamento ideal e, portanto, você deve defini-lo como `false` .

<a name="create-uicontextualaction"></a>

### <a name="create-a-uicontextualaction"></a>Criar um `UIContextualAction`

A ação contextual é onde você realmente cria a ação que será exibida quando o usuário passar uma linha de tabela.

Para inicializar uma ação, você deve fornecer um título e um `UIContextualActionStyle` `UIContextualActionHandler` . O `UIContextualActionHandler` usa três parâmetros: uma ação, a exibição na qual a ação foi exibida e um manipulador de conclusão:

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

Várias propriedades visuais, como a cor do plano de fundo ou a imagem da ação, podem ser editadas. O trecho de código acima demonstra como adicionar uma imagem à ação e definir sua cor de plano de fundo como azul.

Depois que as ações contextuais tiverem sido criadas, elas poderão ser usadas para inicializar o `UISwipeActionsConfiguration` no `GetLeadingSwipeActionsConfiguration` método.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Para definir uma ou mais ações de linha personalizadas para um `UITableView` , você precisará criar uma instância da `UITableViewDelegate` classe e substituir o `EditActionsForRow` método. Por exemplo:

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

O `UITableViewRowAction.Create` método estático é usado para criar um novo `UITableViewRowAction` que exibirá um botão **Olá** quando o usuário passar o dedo para a esquerda horizontalmente em uma linha da tabela. Posteriormente, uma nova instância do `TableDelegate` será criada e anexada ao `UITableView` . Por exemplo:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando o código acima for executado e o usuário passar o dedo para a esquerda em uma linha de tabela, o botão **Hi** será exibido em vez do botão **excluir** , que é exibido por padrão:

[![O botão Olá que está sendo exibido em vez do botão excluir](row-action-images/action01.png)](row-action-images/action01.png#lightbox)

Se o usuário tocar no botão **Olá** , ele será `Hello World!` gravado no console do no Visual Studio para Mac ou no Visual Studio quando o aplicativo for executado no modo de depuração.

## <a name="related-links"></a>Links Relacionados

- [TableSwipeActions (exemplo)](/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables (exemplo)](/samples/xamarin/ios-samples/workingwithtables)