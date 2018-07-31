---
title: Arrastar e soltar no xamarin. IOS
description: Este documento descreve como implementar o arrastar e soltar em aplicativos xamarin. IOS usando as APIs apresentadas no iOS 11. Em particular, discute habilitando arrastar e soltar no UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/05/2017
ms.openlocfilehash: bc58c866a4a754bccea8d851f79e73fe5a415eed
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351129"
---
# <a name="drag-and-drop-in-xamarinios"></a>Arrastar e soltar no xamarin. IOS

_Implementação de arrastar e soltar para iOS 11_

iOS 11 inclui arraste e solte o suporte para copiar dados entre aplicativos no iPad. Os usuários podem selecionar e arrastar todos os tipos de conteúdo de aplicativos posicionadas lado a lado, ou arrastando sobre um ícone de aplicativo que irá disparar o aplicativo para abrir e permitir que os dados a serem removidos:

![Arraste e solte o exemplo de aplicativo personalizado para aplicativo de anotações](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Arrastar e soltar só está disponível dentro do mesmo aplicativo no iPhone.

Considere a possibilidade de dar suporte a arrastar e soltar operações em qualquer lugar conteúdo pode ser criado ou editado:

- Controles de texto oferecem suporte a arrastar e soltar para todos os aplicativos compilados no iOS 11, sem qualquer trabalho adicional.
- Modos de exibição de tabela e modos de exibição de coleção incluem melhorias no iOS 11 que simplificam a adição de arrastar e soltar comportamento.
- Qualquer outra exibição pode ser feita para dar suporte a arrastar e soltar com personalização adicional.

Quando a adição de arrastar e soltar dá suporte aos seus aplicativos, você pode fornecer diferentes níveis de fidelidade de conteúdo; Por exemplo, você pode fornecer um texto formatado e a versão de texto sem formatação dos dados para que o aplicativo receptor possa escolher a que se adapta melhor o destino de arrastar. Também é possível personalizar a visualização de arrastar e também para habilitar arrastar vários itens ao mesmo tempo.

## <a name="drag-and-drop-with-text-controls"></a>Arrastar e soltar com controles de texto

`UITextView` e `UITextField` automaticamente dão suporte a arrastar o texto selecionado-out e descartar o conteúdo de texto.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Arrastar e soltar com UITableView

`UITableView` tem tratamento interno para arrastar e soltar as interações com linhas da tabela, exigindo apenas alguns métodos para habilitar o comportamento padrão.

Há duas interfaces envolvidas:

- `IUITableViewDragDelegate` – Informações de pacotes quando uma operação de arrastar é iniciada no modo de exibição de tabela.
- `IUITableViewDropDelegate` – Processa informações quando uma operação de soltar está sendo tentada e concluída.

No [amostra DragAndDropTableView](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) essas duas interfaces são implementadas no `UITableViewController` classe, juntamente com a fonte de dados e de delegado. Que estão atribuídas a `ViewDidLoad` método:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

O código mínimo necessário para essas duas interfaces é explicado abaixo.

### <a name="table-view-drag-delegate"></a>Delegado de arrastar do modo de exibição de tabela

O único método _necessária_ dar suporte a arrastar uma linha de uma exibição de tabela é `GetItemsForBeginningDragSession`. Se o usuário começa a arrastar uma linha, esse método será chamado.

Uma implementação é mostrada abaixo. Ele recupera os dados associados com a linha arrastada, codificá-lo e configura um `NSItemProvider` que determina como os aplicativos manipulará a parte "soltar" da operação (por exemplo, se eles podem manipular o tipo de dados, `PlainText`, no exemplo):

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

Há muitos métodos opcionais sobre o delegado de arrastar que pode ser implementado para personalizar o comportamento de arrastar, como fornecer várias representações de dados que podem ser aproveitadas em aplicativos de destino (como o texto formatado como bem como texto sem formatação ou um vetor e versões de bitmap de um desenho). Você também pode fornecer representações de dados personalizado a ser usado ao arrastar e soltar dentro do mesmo aplicativo.

### <a name="table-view-drop-delegate"></a>Delegado de soltar de modo de exibição de tabela

Os métodos no delegado de soltar são chamados quando uma operação de arrastar ocorre em uma exibição de tabela, ou conclui acima dele. Os métodos necessários determinam se os dados podem ser descartados e quais ações são executadas se a operação de soltar é concluída:

- `CanHandleDropSession` – Enquanto uma operação de arrastar estiver em andamento e, potencialmente, que está sendo descartado no aplicativo, este método determina se os dados que está sendo arrastados pode ser descartado.
- `DropSessionDidUpdate` – Enquanto a operação de arrastar estiver em andamento, este método é chamado para determinar qual ação destina-se. Todas as informações de exibição de tabela que está sendo arrastado, a sessão de arrastar e o caminho de índice possíveis podem ser usadas para determinar o comportamento e o feedback visual fornecido ao usuário.
- `PerformDrop` – Quando o usuário conclui a operação de soltar (levantando o dedo), esse método extrai os dados que estão sendo arrastados e modifica o modo de exibição de tabela para adicionar os dados em uma nova linha (ou linhas).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` Indica se o modo de exibição de tabela pode aceitar os dados que estão sendo arrastados. Neste trecho de código, `CanLoadObjects` é usado para confirmar que este modo de exibição de tabela pode aceitar dados de cadeia de caracteres.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

O `DropSessionDidUpdate` método é chamado repetidamente durante a operação de arrastar estiver em andamento, para fornecer dicas visuais para o usuário.

No código a seguir, `HasActiveDrag` é usado para determinar se a operação foi originada no modo de exibição de tabela atual. Nesse caso, apenas linhas simples podem ser movidos.
Se a operação de arrastar for de outra origem, uma operação de cópia será indicada:

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

A intenção de soltar pode ser para inserir uma nova linha, ou adicionar/anexar dados para uma linha existente.

#### <a name="performdrop"></a>PerformDrop

O `PerformDrop` método é chamado quando o usuário conclui a operação e modifica a fonte de dados e o modo de exibição de tabela para refletir os dados soltos.

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

Um código adicional pode ser adicionado de forma assíncrona carregar objetos de dados grandes.

### <a name="testing-drag-and-drop"></a>Teste o arrastar e soltar

Você deve usar um iPad para testar o [exemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Abra o exemplo juntamente com outro aplicativo (por exemplo, anotações) e arraste o texto e linhas entre eles:

![captura de tela da operação de arrastar em andamento](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Links relacionados

- [Arraste e solte as diretrizes de Interface Humana (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Arraste e solte o exemplo de modo de exibição de tabela](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Arraste e solte o exemplo de modo de exibição de coleção](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Introdução ao arrastar e soltar (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Arrastar e soltar com a coleta e a tabela de exibição (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/223/)
