---
title: Gestos do Xamarin.Forms
description: Este guia explica como os reconhecedores de gestos do Xamarin.Forms podem ser usados para detectar a interação do usuário com exibições em um aplicativo Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106176"
---
# <a name="xamarinforms-gestures"></a>Gestos do Xamarin.Forms

_Reconhecedores de gestos podem ser usados para detectar a interação do usuário com exibições em um aplicativo Xamarin.Forms._

A classe [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) do Xamarin.Forms dá suporte a gestos de toque, de pinçagem, de panorâmica e de passar o dedo em instâncias de [`View`](xref:Xamarin.Forms.View).

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Adicionando um reconhecedor de gestos de toque](tap.md)

Um gesto de toque é usado para detecção de toque e é reconhecido com a classe [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Adicionando um reconhecedor de gestos de pinçagem](pinch.md)

Um gesto de pinçagem é usado para aplicar zoom interativo e é reconhecido com a classe [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer).

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Adicionando um reconhecedor de gestos de panorâmica](pan.md)

Um gesto de panorâmica é usado para detectar a movimentação dos dedos na tela e aplicar essa movimentação ao conteúdo. Ele é reconhecido com a classe [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer).

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Adicionando um reconhecedor de gestos de passar o dedo](swipe.md)

Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação pelo conteúdo. Gestos de passar o dedo são reconhecidos com a classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer).
