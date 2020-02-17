---
title: TwoPaneView do Xamarin.Forms
description: Este guia explica como usar o TwoPaneView do Xamarin.Forms para otimizar a experiência de aplicativo para dispositivos de tela dupla, como o Surface Duo e o Surface Neo.
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1992a714774dba79e42c82e71af0bfe6aed7feb7
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145500"
---
# <a name="xamarinforms-twopaneview"></a>TwoPaneView do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

Representa um contêiner com duas exibições que dimensionam e posicionam o conteúdo no espaço disponível, seja lado a lado ou de cima para baixo. O TwoPaneView herda da Grade para que a maneira mais fácil de pensar sobre essas propriedades seja como se elas estivessem sendo aplicadas a uma grade

## <a name="set-up-twopaneview"></a>Configurar o TwoPaneView

A propriedade `TwoPaneView` `Source` pode usar um URI ou um caminho de arquivo local. A reprodução será iniciada imediatamente após a abertura da mídia.

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>Entender os modos do TwoPaneView

Somente um desses modos pode estar ativo

- `SinglePane` somente um painel está visível atualmente
- `Wide` os dois painéis estão dispostos horizontalmente. Um painel está à esquerda e o outro, à direita. Quando em duas telas, esse é o modo no qual o dispositivo está em modo retrato
- `Tall` os dois painéis estão dispostos verticalmente. Um painel está na parte superior e o outro, na parte inferior. Quando em duas telas, esse é o modo no qual o dispositivo está em modo paisagem

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>Controlar o TwoPaneView quando ele estiver apenas em uma tela

As propriedades a seguir só serão relevantes quando o TwoPaneView estiver ocupando uma tela. Se o TwoPaneView for estendido entre duas telas, essas propriedades ficarão inativas.

- `MinTallModeHeight` indica a altura mínima que o controle deve ter para ser inserido no modo alto
- `MinWideModeWidth` indica a largura mínima que o controle deve ter para ser inserido no modo largo
- `Pane1Length` define a largura do Painel1 em modo Largo, a altura do Painel1 no modo Alto e não tem alteração no modo SinglePane
- `Pane2Length` define a largura do Painel2 em modo Largo, a altura do Painel2 no modo Alto e não tem alteração no modo SinglePane

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>Propriedades que se aplicam quando em uma tela ou duas

- `TallModeConfiguration` quando no modo alto, isso indica a organização Esquerda/Direita ou se você deseja apenas um painel visível, conforme definido pelo TwoPaneViewPriority
- `WideModeConfiguration` quando no modo alto, isso indica a organização Superior/Inferior ou se você deseja apenas um painel visível, conforme definido pelo TwoPaneViewPriority
- `PanePriority` seja para mostrar o Painel1 ou Painel2 se estiver no modo SinglePane

## <a name="related-links"></a>Links relacionados

- [DualScreen (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
