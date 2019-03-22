---
title: Xamarin. Forms CollectionView
description: O CollectionView é uma exibição flexível e de alto desempenho para apresentações de listas de dados usando as especificações de layout diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
---

# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

![Visualizar](~/media/shared/preview.png)

> [!IMPORTANT]
> O `CollectionView` atualmente é uma visualização e não tem algumas das suas funcionalidades planejada. Além disso, a API pode mudar conforme a implementação for concluída.

`CollectionView` é um modo de exibição para apresentar as listas de dados usando as especificações de layout diferente. Tem como objetivo fornecer uma mais flexível e alternativa de alto desempenho para o [ `ListView` ](xref:Xamarin.Forms.ListView). Enquanto o `CollectionView` e `ListView` APIs são semelhantes, existem algumas diferenças importantes:

- `CollectionView` tem um modelo de layout flexível que permite que os dados a ser apresentado verticalmente ou horizontalmente, em uma lista ou uma grade.
- `CollectionView` não tem nenhum conceito de células. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista.
- `CollectionView` utiliza automaticamente a virtualização fornecida pelos controles nativos subjacentes.
- `CollectionView` reduz a superfície de API do [ `ListView` ](xref:Xamarin.Forms.ListView). Muitas propriedades e eventos do `ListView` não estão presentes no `CollectionView`.
- `CollectionView` não inclui separadores internos.

`CollectionView` está disponível nas versões de pré-lançamento de 4.0 do xamarin. Forms. No entanto, ele é atualmente experimental e só pode ser usado, adicionando a seguinte linha de código para seus `AppDelegate` classe no iOS, ou para seus `MainActivity` classe no Android, antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!NOTE]
> `CollectionView` só está disponível no iOS e Android.

## <a name="populate-collectionview-with-datapopulate-datamd"></a>[Preencher CollectionView com dados](populate-data.md)

Um `CollectionView` é preenchido com dados, definindo seu `ItemsSource` propriedade para qualquer coleção que implemente `IEnumerable`. A aparência de cada item na lista pode ser definida ao configurar o `ItemTemplate` propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="specify-collectionview-layoutlayoutmd"></a>[Especifique o layout de CollectionView](layout.md)

Por padrão, um `CollectionView` exibirá seus itens em uma lista vertical. No entanto, grades e listas verticais e horizontais podem ser especificadas.

## <a name="set-collectionview-selection-modeselectionmd"></a>[Definir o modo de seleção CollectionView](selection.md)

Por padrão, `CollectionView` seleção está desabilitada. No entanto, a seleção única e múltipla pode ser habilitada.

## <a name="display-an-emptyview-when-data-is-unavailableemptyviewmd"></a>[Exibir um EmptyView quando dados não estão disponíveis](emptyview.md)

No `CollectionView`, um modo de exibição vazio pode ser especificado que fornece comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou vários modos de exibição.

## <a name="scroll-an-item-into-viewscrollingmd"></a>[Rolar um item na exibição](scrolling.md)

Quando um dedo do usuário para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens são totalmente exibidas. Além disso, `CollectionView` define dois `ScrollTo` métodos, que rolagem programaticamente os itens na exibição. Uma das sobrecargas rola o item no índice especificado na exibição, enquanto o outro rola o item especificado na exibição.
