---
title: Controles deslizantes, comutadores e controles segmentados no xamarin. IOS
description: Este documento discute slides, comutadores e controles segmentados em xamarin. IOS, que descrevem como trabalhar com eles por meio de programação e o Designer do iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 2ed14752cc5906b68d277b4f492875f7e281b053
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029879"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Controles deslizantes, comutadores e controles segmentados no xamarin. IOS

<a name="Sliders" />

## <a name="sliders"></a>Controles deslizantes

Permite o controle deslizante simples seleção de um valor numérico em um intervalo. O controle padrão é um valor entre 0 e 1, mas esses limites podem ser personalizados.

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Captura de tela a seguir mostra as propriedades que podem ser editadas no Designer:

 [![](slider-switch-segmented-controls-images/image26a.png "Propriedades do controle deslizante")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Você pode definir esses valores no código, conforme mostrado abaixo, incluindo conectando um manipulador para exibir o valor selecionado no momento em um `UILabel` controle:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

Você também pode personalizar a aparência visual do controle deslizante por configuração

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

O controle deslizante personalizado tem esta aparência:

 [![](slider-switch-segmented-controls-images/image27a.png "Controle deslizante personalizado")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Atualmente, há uma [bug](https://stackoverflow.com/a/19496179) causando o `ThumbTint` para não processar em tempo de execução, conforme o esperado. Você pode adicionar a seguinte linha de código **antes de** o código acima como uma solução alternativa. [[Origem](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Você pode usar qualquer imagem, pois ele será substituído, mas verifique se ele é colocado _em_ o diretório de recursos e é chamado em seu código.

<a name="Switch" />

## <a name="switch"></a>Alternar

o iOS usa o `UISwitch` como um valor booliano que pode ser representado por um botão de opção em outras plataformas de entrada. O usuário pode manipular o controle, movendo o *thumb* entre o **ativar/desativar** posições.

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

A aparência do comutador pode ser personalizada do **painel de propriedades** do designer, que permitirá a você controlar o estado padrão, **ativar/desativar tonalidade** cores e uma **on/off imagem**. Isso é ilustrado na imagem abaixo:

 [![](slider-switch-segmented-controls-images/image29a.png "Propriedades do comutador")](slider-switch-segmented-controls-images/image29a.png#lightbox)

As propriedades do comutador também podem ser definidas no código, por exemplo, o código a seguir mostrará um comutador com o valor padrão de `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Controles segmentados

Um controle segmentado é uma maneira organizada para permitir que os usuários interajam com um pequeno número de opções. Ele é disposto horizontalmente e cada segmento funciona como um botão separado. Ao usar o Designer, o controle segmentado podem ser encontrado em **caixa de ferramentas > controles**e deve se parecer com a imagem a seguir:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Controle segmentado")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Permite que um recurso exclusivo do Designer para cada segmento a ser selecionados individualmente na superfície de design, conforme ilustrado abaixo:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Controle segmentado")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Isso permite que o painel de propriedades a ser usado para controlar com mais precisão as propriedades de cada segmento. Você pode ver as propriedades editáveis na captura de tela abaixo:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Controle segmentado")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Ele deve observar que o estilo de controle segmentado foi preterido no iOS7 e, portanto, ajuste as opções para isso em um aplicativo iOS7 não terá efeito.

## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/Controls/)
- [Controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
