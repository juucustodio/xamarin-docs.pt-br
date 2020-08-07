---
title: Xamarin.Formslayout de tela dupla
description: Este guia explica como usar Xamarin.Forms o TwoPaneView para otimizar sua experiência de aplicativo para dispositivos de tela dupla, como Surface Duo e Surface neo.
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 734dea456af56f4103691e0368ae72202bce9556
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918243"
---
# <a name="no-locxamarinforms-twopaneview-layout"></a>Xamarin.FormsLayout de TwoPaneView

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

A classe `TwoPaneView` representa um contêiner com duas exibições que dimensionam e posicionam o conteúdo no espaço disponível, lado a lado ou de cima para baixo. `TwoPaneView` herda de `Grid`, portanto, a maneira mais fácil de entender essas propriedades é imaginar que elas estão sendo aplicadas a uma grade.

## <a name="set-up-twopaneview"></a>Configurar o TwoPaneView

Siga estas instruções para criar um layout de tela dupla em seu aplicativo:

1. Siga as instruções de [introdução](index.md) para adicionar o NuGet e configurar a classe do Android `MainActivity` .
1. Comece com um básico `TwoPaneView` usando o seguinte XAML:

    ```xaml
    <ContentPage
        xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
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

> [!TIP]
> O XAML acima omite muitos atributos comuns do `ContentPage` elemento. Ao adicionar um `TwoPaneView` ao seu aplicativo, lembre-se de declarar o `xmlns:dualScreen` namespace conforme mostrado.

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

- `TallModeConfiguration`indica, quando no modo alto, a organização superior/inferior ou se você quiser apenas um único painel visível, conforme definido pelo TwoPaneViewPriority.
- `WideModeConfiguration`indica, quando em modo largo, a organização esquerda/direita ou se você quiser apenas um único painel visível, conforme definido pelo TwoPaneViewPriority.
- `PanePriority` determina se o Pane1 ou o Pane2 deve estar no modo SinglePane.

## <a name="related-links"></a>Links relacionados

- [DualScreen (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
