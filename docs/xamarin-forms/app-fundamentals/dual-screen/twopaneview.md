---
title: Layout de tela dupla do Xamarin. Forms
description: Este guia explica como usar o TwoPaneView do Xamarin.Forms para otimizar a experiência de aplicativo para dispositivos de tela dupla, como o Surface Duo e o Surface Neo.
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: e961dc537d8b793feb3587ec89b69c53ab821088
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160565"
---
# <a name="xamarinforms-dual-screen-layout"></a>Layout de tela dupla do Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/DualScreenDemos)

A classe `TwoPaneView` representa um contêiner com duas exibições que dimensionam e posicionam o conteúdo no espaço disponível, lado a lado ou de cima para baixo. `TwoPaneView` herda de `Grid`, portanto, a maneira mais fácil de entender essas propriedades é imaginar que elas estão sendo aplicadas a uma grade.

## <a name="set-up-twopaneview"></a>Configurar o TwoPaneView

A propriedade `TwoPaneView.Source` pode usar um URI ou um caminho de arquivo local. A reprodução será iniciada imediatamente após a abertura da mídia:

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>Entender os modos do TwoPaneView

Somente um destes modos pode estar ativo:

- `SinglePane` somente um painel está visível no momento.
- `Wide` os dois painéis estão dispostos horizontalmente. Um painel está à esquerda e o outro, à direita. Quando estiver em duas telas, esse será o modo do dispositivo na orientação retrato.
- `Tall` os dois painéis estão dispostos verticalmente. Um painel está na parte superior e o outro na parte inferior. Quando estiver em duas telas, esse será o modo do dispositivo na orientação paisagem.

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>Controlar o TwoPaneView quando ele estiver apenas em uma tela

As propriedades a seguir se aplicam quando `TwoPaneView` estiver ocupando uma única tela:

- `MinTallModeHeight` indica a altura mínima que o controle deverá ter para entrar no modo alto.
- `MinWideModeWidth` indica a largura mínima que o controle deverá ter para entrar no modo largo.
- `Pane1Length` define a largura de Pane1 no modo largo, a altura de Pane1 no modo alto e não tem efeito no modo SinglePane.
- `Pane2Length` define a largura de Pane2 no modo largo, a altura de Pane2 no modo alto e não tem efeito no modo SinglePane.

> [!IMPORTANT]
> Caso `TwoPaneView` se estenda pelas duas telas, essas propriedades não terão nenhum efeito.

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>Propriedades que se aplicam quando em uma tela ou duas

As propriedades a seguir se aplicam quando `TwoPaneView` estiver ocupando uma ou duas telas:

- `TallModeConfiguration` indica, quando estiver no modo alto, a organização esquerda/direita ou apenas um único painel visível se você preferir, conforme definido por TwoPaneViewPriority.
- `WideModeConfiguration` indica, quando estiver modo largo, a organização superior/inferior ou apenas um único painel visível se você preferir, conforme definido por TwoPaneViewPriority.
- `PanePriority` determina se o Pane1 ou o Pane2 deve estar no modo SinglePane.

## <a name="related-links"></a>Links relacionados

- [DualScreen (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/DualScreenDemos)
