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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61184564"
---
# <a name="xamarinforms-gestures"></a>Gestos do Xamarin.Forms

_Reconhecedores de gestos podem ser usados para detectar a interação do usuário com exibições em um aplicativo Xamarin.Forms._

A classe Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) suporta gestos de toque, pinça, panela e deslizamento em [`View`](xref:Xamarin.Forms.View) instâncias.

## <a name="adding-a-tap-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de toque](tap.md)

Um gesto de toque é usado [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) para detecção de toques e é reconhecido com a classe.

## <a name="adding-a-pinch-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de pinçagem](pinch.md)

Um gesto de pinça é usado para [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) realizar zoom interativo e é reconhecido com a classe.

## <a name="adding-a-pan-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de panorâmica](pan.md)

Um gesto de panela é usado para detectar o movimento dos dedos ao redor [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) da tela e aplicar esse movimento ao conteúdo, e é reconhecido com a classe.

## <a name="adding-a-swipe-gesture-recognizer"></a>[Adicionando um reconhecedor de gestos de passar o dedo](swipe.md)

Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação pelo conteúdo. Gestos de furto são [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) reconhecidos com a classe.
