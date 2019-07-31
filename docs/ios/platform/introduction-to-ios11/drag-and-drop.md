---
title: Arrastar e soltar no Xamarin. iOS
description: Este documento descreve como implementar o recurso de arrastar e soltar em aplicativos Xamarin. iOS usando as APIs introduzidas no iOS 11. Em particular, ele aborda a habilitação de arrastar e soltar em UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/05/2017
ms.openlocfilehash: cb982b1cd2340262101ff09bce2c37c69864b8dc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656478"
---
# <a name="drag-and-drop-in-xamarinios"></a>Arrastar e soltar no Xamarin. iOS

_Implementando arrastar e soltar para iOS 11_

o iOS 11 inclui suporte para arrastar e soltar para copiar dados entre aplicativos no iPad. Os usuários podem selecionar e arrastar todos os tipos de conteúdo de aplicativos posicionados lado a lado ou arrastando-se sobre um ícone de aplicativo que disparará o aplicativo para abrir e para permitir que os dados sejam descartados:

![Arrastar e soltar o exemplo do aplicativo personalizado no aplicativo do Notes](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> O recurso arrastar e soltar só está disponível no mesmo aplicativo no iPhone.

Considere oferecer suporte a operações de arrastar e soltar em qualquer lugar que o conteúdo possa ser criado ou editado:

- Os controles de texto dão suporte ao recurso arrastar e soltar para todos os aplicativos criados no iOS 11, sem nenhum trabalho adicional.
- Exibições de tabela e exibições de coleção incluem aprimoramentos no iOS 11 que simplificam a adição do comportamento de arrastar e soltar.
- Qualquer outra exibição pode ser feita para dar suporte a arrastar e soltar com personalização adicional.

Ao adicionar suporte para arrastar e soltar aos seus aplicativos, você pode fornecer diferentes níveis de fidelidade de conteúdo; por exemplo, você pode fornecer um texto formatado e uma versão de texto sem formatação dos dados para que o aplicativo de recebimento possa escolher o que se adapta melhor ao destino de arrastar. Também é possível personalizar a visualização de arrastar e também para habilitar o arrasto de vários itens de uma só vez.

## <a name="drag-and-drop-with-text-controls"></a>Arrastar e soltar com controles de texto

`UITextView`e `UITextField` dão suporte automaticamente ao arrastar o texto selecionado para fora e remover o conteúdo de texto no.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Arrastar e soltar com UITableView

`UITableView`tem manipulação interna para arrastar e soltar interações com linhas de tabela, exigindo apenas alguns métodos para habilitar o comportamento padrão.

Há duas interfaces envolvidas:

- `IUITableViewDragDelegate`– Informações de pacotes quando um arrastar é iniciado na exibição de tabela.
- `IUITableViewDropDelegate`– Processa informações quando uma queda está sendo tentada e concluída.

No [exemplo de DragAndDropTableView](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview) , essas duas interfaces são implementadas na `UITableViewController` classe, juntamente com o delegado e a fonte de dados. Eles são atribuídos no `ViewDidLoad` método:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

O código mínimo necessário para essas duas interfaces é explicado abaixo.

### <a name="table-view-drag-delegate"></a>Delegar arrastar de exibição de tabela

O único método _necessário_ para dar suporte ao arrastar uma linha de uma exibição `GetItemsForBeginningDragSession`de tabela é. Se o usuário começar a arrastar uma linha, esse método será chamado.

Uma implementação é mostrada abaixo. Ele recupera os dados associados à linha arrastada, codifica-os e configura um `NSItemProvider` que determina como os aplicativos tratarão a parte "descartar" da operação (por exemplo, se eles podem manipular o tipo de dados, `PlainText`, no exemplo):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

Há muitos métodos opcionais no delegado de arrastar que podem ser implementados para personalizar o comportamento de arrastar, como fornecer várias representações de dados que podem ser aproveitadas em aplicativos de destino (como texto formatado, bem como texto sem formatação ou um vetor e versões de bitmap de um desenho). Você também pode fornecer representações de dados personalizadas a serem usadas ao arrastar e soltar no mesmo aplicativo.

### <a name="table-view-drop-delegate"></a>Delegar remoção de exibição de tabela

Os métodos no delegado de descarte são chamados quando uma operação de arrastar ocorre em uma exibição de tabela ou é concluída acima dela. Os métodos necessários determinam se os dados podem ser removidos e quais ações serão executadas se a remoção for concluída:

- `CanHandleDropSession`– Embora uma operação de arrastar esteja em andamento e possa ser cancelada no aplicativo, esse método determina se os dados que estão sendo arrastados podem ser descartados.
- `DropSessionDidUpdate`– Enquanto a operação de arrastar está em andamento, esse método é chamado para determinar a ação desejada. As informações da exibição de tabela que estão sendo arrastadas, a sessão de arrastar e o caminho de índice possível podem ser usadas para determinar o comportamento e os comentários visuais fornecidos ao usuário.
- `PerformDrop`– Quando o usuário conclui o descarte (levantando seu dedo), esse método extrai os dados que estão sendo arrastados e modifica a exibição de tabela para adicionar os dados em uma nova linha (ou linhas).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession`indica se a exibição de tabela pode aceitar os dados que estão sendo arrastados. Neste trecho de código, `CanLoadObjects` é usado para confirmar que este modo de exibição de tabela pode aceitar dados de cadeia de caracteres.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

O `DropSessionDidUpdate` método é chamado repetidamente enquanto a operação de arrastar está em andamento, para fornecer indicações visuais ao usuário.

No código abaixo, `HasActiveDrag` é usado para determinar se a operação foi originada no modo de exibição de tabela atual. Nesse caso, apenas as linhas únicas podem ser movidas.
Se o arrastar for de outra fonte, uma operação de cópia será indicada:

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

A operação de soltar pode ser uma `Cancel`das `Move`, ou `Copy`.

A intenção pode ser inserir uma nova linha ou adicionar/acrescentar dados a uma linha existente.

#### <a name="performdrop"></a>PerformDrop

O `PerformDrop` método é chamado quando o usuário conclui a operação e modifica a exibição de tabela e a fonte de dados para refletir os dados descartados.

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

O código adicional pode ser adicionado para carregar objetos de dados grandes de forma assíncrona.

### <a name="testing-drag-and-drop"></a>Teste de arrastar e soltar

Você deve usar um iPad para testar o [exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview).
Abra o exemplo junto com outro aplicativo (como observações) e arraste linhas e texto entre eles:

![captura de tela da operação de arrastar em andamento](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Links relacionados

- [Diretrizes de interface humana de arrastar e soltar (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Exemplo de exibição de tabela de arrastar e soltar](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [Arrastar e soltar exibição de coleção de exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [Introdução ao recurso de arrastar e soltar (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Arrastar e soltar com coleção e exibição de tabela (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/223/)
