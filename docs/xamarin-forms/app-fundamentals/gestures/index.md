---
title: Xamarin.Formsgestos
description: Este guia explica como os Xamarin.Forms reconhecedores de gestos podem ser usados para detectar a interação do usuário com exibições em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7528afd0971cf06eb69df4ed7c08c3fd6dcc9e22
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917829"
---
# <a name="no-locxamarinforms-gestures"></a>Xamarin.Formsgestos

_Os reconhecedores de gestos podem ser usados para detectar a interação do usuário com exibições em um Xamarin.Forms aplicativo._

A Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) classe dá suporte aos gestos tocar, pinçar, deslocar, deslizar e arrastar e soltar em [`View`](xref:Xamarin.Forms.View) instâncias.

## <a name="add-a-tap-gesture-recognizer"></a>[Adicionar um reconhecedor de gestos Tap](tap.md)

Um gesto de toque é usado para detecção de toque e é reconhecido com a [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) classe.

## <a name="add-a-pinch-gesture-recognizer"></a>[Adicionar um reconhecedor de gestos de pinçagem](pinch.md)

Um gesto de pinçagem é usado para executar zoom interativo e é reconhecido com a [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) classe.

## <a name="add-a-pan-gesture-recognizer"></a>[Adicionar um reconhecedor de gesto de Pan](pan.md)

Um gesto de panorâmica é usado para detectar a movimentação de dedos em toda a tela e aplicar essa movimentação ao conteúdo e é reconhecido com a [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="add-a-swipe-gesture-recognizer"></a>[Adicionar um reconhecedor de gestos de passar](swipe.md)

Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação pelo conteúdo. Gestos de passar o dedo são reconhecidos com a [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe.

## <a name="add-a-drag-and-drop-gesture-recognizer"></a>[Adicionar um reconhecedor de gestos de arrastar e soltar](drag-and-drop.md)

Um gesto de arrastar e soltar permite que os itens e seus pacotes de dados associados sejam arrastados de um local na tela para outro local usando um gesto contínuo. Os gestos de arrastar são reconhecidos com a `DragGestureRecognizer` classe e os gestos de soltar são reconhecidos com a `DropGestureRecognizer` classe.
