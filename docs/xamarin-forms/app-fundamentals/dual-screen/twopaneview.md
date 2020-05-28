---
title: Xamarin.Formslayout de tela dupla
description: Este guia explica como usar Xamarin.Forms o TwoPaneView para otimizar sua experiência de aplicativo para dispositivos de tela dupla, como Surface Duo e Surface neo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28d4b3da44cc1a022b70c0de0720be747e047f9f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138886"
---
# <a name="xamarinforms-dual-screen-layout"></a>Xamarin.Formslayout de tela dupla

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

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

- [DualScreen (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
