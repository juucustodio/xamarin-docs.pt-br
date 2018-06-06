---
title: Arrastar e soltar no xamarin
description: Este documento descreve como implementar arrastar e soltar em aplicativos xamarin usando as APIs do iOS 11. Em particular, ele discute Habilitar arrastar e soltar no UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/05/2016
ms.openlocfilehash: 7c41f96dae88047e64ec1e74838e3efab55958cc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786958"
---
# <a name="drag-and-drop-in-xamarinios"></a>Arrastar e soltar no xamarin

_Implementação de arrastar e soltar para iOS 11_

iOS 11 inclui arrastar e soltar para copiar dados entre aplicativos no iPad. Os usuários podem selecione e arraste a todos os tipos de conteúdo de aplicativos posicionadas lado a lado, ou arrastando um ícone de aplicativo que irá disparar o aplicativo para abrir e permitir que os dados a serem removidos:

![Arrastar e soltar o exemplo de aplicativo personalizado no aplicativo de anotações](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Arrastar e soltar só está disponível dentro do mesmo aplicativo no iPhone.

Considere a possibilidade de dar suporte a arrastar e soltar operações em qualquer lugar conteúdo pode ser criado ou editado:

- Controles de texto oferecem suporte a arrastar e soltar para todos os aplicativos criados com iOS 11, sem qualquer trabalho adicional.
- Modos de exibição de tabela e modos de exibição de coleção incluem melhorias no iOS 11 que simplificam a adição de arrastar e soltar comportamento.
- Qualquer outro modo de exibição pode ser feito para dar suporte a arrastar e soltar com personalização adicional.

Quando a adição de arrastar e soltar oferecem suporte aos seus aplicativos, você pode fornecer níveis diferentes de fidelidade de conteúdo; Por exemplo, você pode fornecer um texto formatado e a versão de texto sem formatação dos dados para que o aplicativo receptor pode escolher a que melhor se encaixa no destino de arrastar. Também é possível personalizar a visualização de arrastar e habilitar arrastar vários itens de uma vez.

## <a name="drag-and-drop-with-text-controls"></a>Arrastar e soltar com controles de texto

`UITextView` e `UITextField` automaticamente dão suporte a arrastar o texto selecionado-out e descartar o conteúdo de texto.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Arrastar e soltar com UITableView

`UITableView` possui tratamento interno para arrastar e soltar interações com linhas da tabela, exigindo somente alguns métodos para habilitar o comportamento padrão.

Há duas interfaces envolvidas:

- `IUITableViewDragDelegate` – Informações de pacotes quando um arraste é iniciado no modo de exibição de tabela.
- `IUITableViewDropDelegate` – Processa informações quando uma remoção está sendo tentada e concluídos.

No [DragAndDropTableView exemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) essas duas interfaces são implementados no `UITableViewController` classe, juntamente com o delegado e fonte de dados. Que estão atribuídas a `ViewDidLoad` método:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

O mínimo de código necessário para essas duas interfaces é explicado abaixo.

### <a name="table-view-drag-delegate"></a>Delegado de arrastar de modo de exibição de tabela

O único método _necessária_ dar suporte a arrastar uma linha de uma exibição de tabela é `GetItemsForBeginningDragSession`. Se o usuário começa a arrastar uma linha, este método será chamado.

Uma implementação é mostrada abaixo. Recupera os dados associados à linha arrastada, codifica e configura um `NSItemProvider` que determina como os aplicativos tratará a parte "drop" da operação (por exemplo, se eles possam lidar com o tipo de dados, `PlainText`, no exemplo):

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

Há vários métodos opcionais sobre o delegado de arrastar que pode ser implementado para personalizar o comportamento de arrastar, como fornecer várias representações de dados que podem ser executadas aproveitar em aplicativos de destino (como texto formatado como bem como texto sem formatação ou um vetor e versões de bitmap de um desenho). Você também pode fornecer representações de dados personalizado a ser usado quando arrastando e soltando dentro do mesmo aplicativo.

### <a name="table-view-drop-delegate"></a>Delegado de Drop de exibição de tabela

Os métodos sobre o delegado de soltar são chamados quando uma operação de arrastar ocorre em uma tabela, exibição ou conclusão acima dele. Os métodos necessários determinam se os dados podem ser descartados e quais ações são executadas se a remoção for concluída:

- `CanHandleDropSession` – Enquanto um arrastar está em andamento e potencialmente descartadas no aplicativo, este método determina se os dados que está sendo arrastados tem permissão para ser removido.
- `DropSessionDidUpdate` – Enquanto a operação de arrastar estiver em andamento, esse método é chamado para determinar a ação que é destinada. Todas as informações de exibição de tabela que está sendo arrastado, a sessão de arrastar e o caminho de possível índice podem ser usadas para determinar o comportamento e comentários visuais fornecidas para o usuário.
- `PerformDrop` – Quando o usuário conclui o descarte (levantar o dedo), esse método extrai os dados que está sendo arrastados e modifica o modo de exibição de tabela para adicionar os dados em uma nova linha (ou linhas).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` Indica se o modo de exibição de tabela pode aceitar os dados que está sendo arrastados. No trecho de código, `CanLoadObjects` é usado para confirmar que esse modo de exibição de tabela pode aceitar dados de cadeia de caracteres.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

O `DropSessionDidUpdate` método é chamado repetidamente durante a operação de arrastar estiver em andamento, para fornecer dicas visuais para o usuário.

No código a seguir, `HasActiveDrag` é usado para determinar se a operação foi originado no modo de exibição de tabela atual. Nesse caso, somente as linhas simples podem ser movidos.
Se a operação de arrastar é de outra origem, uma operação de cópia será indicada:

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

A operação de soltar pode ser um dos `Cancel`, `Move`, ou `Copy`.

A tentativa de descarte pode ser para inserir uma nova linha, ou adicionar/anexar dados para uma linha existente.

#### <a name="performdrop"></a>PerformDrop

O `PerformDrop` método é chamado quando o usuário conclui a operação e modifica a fonte de exibição e os dados de tabela para refletir os dados removidos.

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

Código adicional pode ser adicionado de forma assíncrona carregar objetos de dados grandes.

### <a name="testing-drag-and-drop"></a>Teste de arrastar e soltar

Você deve usar um iPad para testar o [exemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Abra o exemplo juntamente com outro aplicativo (como notas) e arraste as linhas e texto entre eles:

![captura de tela da operação de arrastar em andamento](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Links relacionados

- [Arrastar e soltar diretrizes de Interface Humana (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Arraste e solte o exemplo de modo de exibição de tabela](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Arraste e solte o exemplo de modo de exibição de coleção](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Introdução ao arrastar e soltar (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Arrastar e soltar com coleta e a tabela de exibição (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/223/)
