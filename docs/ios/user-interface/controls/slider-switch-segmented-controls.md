---
title: Controles deslizantes, comutadores e controle segmentados no Xamarin. iOS
description: Este documento aborda os slides, os comutadores e os controles segmentados no Xamarin. iOS, descrevendo como trabalhar com eles de forma programática e no designer do iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 763165f1e09f847745b820987f8dbbae8f834fd7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021959"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Controles deslizantes, comutadores e controle segmentados no Xamarin. iOS

<a name="Sliders" />

## <a name="sliders"></a>Controles deslizantes

O controle deslizante permite a seleção simples de um valor numérico dentro de um intervalo. O padrão de controle é um valor entre 0 e 1, mas esses limites podem ser personalizados.

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

A captura de tela a seguir mostra as propriedades que são editáveis no designer:

 [![](slider-switch-segmented-controls-images/image26a.png "Slider Properties")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Você pode definir esses valores no código, conforme mostrado abaixo, incluindo a conexão de um manipulador para exibir o valor selecionado no momento em um controle de `UILabel`:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

Você também pode personalizar a aparência visual do controle deslizante definindo

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

O controle deslizante personalizado tem esta aparência:

 [![](slider-switch-segmented-controls-images/image27a.png "Custom Slider")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Atualmente, há um [bug](https://stackoverflow.com/a/19496179) que faz com que o `ThumbTint` não seja processado em tempo de execução conforme o esperado. Você pode adicionar a seguinte linha de código **antes** do código acima como uma solução alternativa. [[Origem](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Você pode usar qualquer imagem, pois ela será substituída, mas certifique-se de que ela seja colocada _no_ diretório de recursos e seja chamada em seu código.

<a name="Switch" />

## <a name="switch"></a>Alternar

o iOS usa o `UISwitch` como uma entrada booliana que pode ser representada por um botão de opção em outras plataformas. O usuário pode manipular o controle movendo o *polegar* entre as posições **Ativar/desativar** .

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

A aparência da opção pode ser personalizada na **painel de propriedades** do designer, o que permitirá controlar o estado padrão, **Ativar/desativar** as cores de tonalidade e uma **imagem on/off**. Isso é ilustrado na imagem abaixo:

 [![](slider-switch-segmented-controls-images/image29a.png "Switch Properties")](slider-switch-segmented-controls-images/image29a.png#lightbox)

As propriedades da opção também podem ser definidas no código, por exemplo, o código a seguir mostrará uma opção com o valor padrão de `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />

## <a name="segmented-controls"></a>Controles segmentados

Um controle segmentado é uma maneira organizada de permitir que os usuários interajam com um pequeno número de opções. Ele é disposto horizontalmente e cada segmento funciona como um botão separado. Ao usar o designer, o controle segmentado pode ser encontrado em **caixa de ferramentas > controles**e deve ser semelhante à imagem a seguir:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Um recurso exclusivo do designer permite que cada segmento seja selecionado individualmente na superfície de design, conforme ilustrado abaixo:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Isso permite que o Painel de Propriedades seja usado para controlar com mais precisão as propriedades de cada segmento. Você pode ver as propriedades editáveis na captura de tela abaixo:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Deve-se observar que o estilo de controle segmentado foi preterido em iOS7 e, portanto, ajustar as opções para isso em um aplicativo iOS7 não terá nenhum efeito.

## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
