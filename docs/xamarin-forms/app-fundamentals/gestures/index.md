---
title: Gestos do xamarin. Forms
description: Este guia explica como os reconhecedores de gestos do xamarin. Forms podem ser usados para detectar a interação do usuário com modos de exibição em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106176"
---
# <a name="xamarinforms-gestures"></a>Gestos do xamarin. Forms

_Reconhecedores de gestos podem ser usados para detectar a interação do usuário com modos de exibição em um aplicativo xamarin. Forms._

O xamarin. Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) classe dá suporte a toque, pinch, Panorâmica e gestos de passar o dedo na [ `View` ](xref:Xamarin.Forms.View) instâncias.

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Adicionando um reconhecedor de gestos de toque](tap.md)

Um gesto de tocar é usado para detecção de toque e é reconhecido com o [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) classe.

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Adicionando um reconhecedor de gestos de aperto](pinch.md)

Um gesto de pinçagem é usado para executar o zoom interativa e é reconhecido com o [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) classe.

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Adicionando um reconhecedor de gestos de Panorâmica](pan.md)

Um gesto de Panorâmica é usado para detectar a movimentação dos dedos na tela e aplicar essa movimentação para o conteúdo e é reconhecido com o [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Adicionando um reconhecedor de gestos de passar o dedo](swipe.md)

Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação por meio do conteúdo. Gestos de passar o dedo são reconhecidos como o [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) classe.
