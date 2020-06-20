---
title: Xamarin.Formsgestos
description: Este guia explica como os Xamarin.Forms reconhecedores de gestos podem ser usados para detectar a interação do usuário com exibições em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1e93f74ab8ef6d63213a8fbdc7ec45a794cf55
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137871"
---
# <a name="xamarinforms-gestures"></a>Xamarin.Formsgestos

_Os reconhecedores de gestos podem ser usados para detectar a interação do usuário com exibições em um Xamarin.Forms aplicativo._

A Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) classe dá suporte a gestos de toque, pinçar, deslocar e deslizar em [`View`](xref:Xamarin.Forms.View) instâncias.

## <a name="adding-a-tap-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de toque](tap.md)

Um gesto de toque é usado para detecção de toque e é reconhecido com a [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) classe.

## <a name="adding-a-pinch-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de pinçagem](pinch.md)

Um gesto de pinçagem é usado para executar zoom interativo e é reconhecido com a [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) classe.

## <a name="adding-a-pan-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de panorâmica](pan.md)

Um gesto de panorâmica é usado para detectar a movimentação de dedos em toda a tela e aplicar essa movimentação ao conteúdo e é reconhecido com a [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="adding-a-swipe-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de passar o dedo](swipe.md)

Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação pelo conteúdo. Gestos de passar o dedo são reconhecidos com a [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe.
