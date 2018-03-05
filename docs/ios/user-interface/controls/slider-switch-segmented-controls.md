---
title: Controles deslizantes, comutadores e controles segmentados
ms.topic: article
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 1c24b1faf7b108466d6e93ffae8112d0dea6d844
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="sliders-switches-and-segmented-controls"></a>Controles deslizantes, comutadores e controles segmentados

<a name="Sliders" />


## <a name="sliders"></a>Controles deslizantes

O controle deslizante permite simples seleção de um valor numérico em um intervalo. O padrão para um valor entre 0 e 1, mas esses limites podem ser personalizados.

 [ ![](slider-switch-segmented-controls-images/image25a.png "Controle deslizante")](slider-switch-segmented-controls-images/image25a.png)

Captura de tela a seguir mostra as propriedades que podem ser editadas no Designer:

 [ ![](slider-switch-segmented-controls-images/image26a.png "Propriedades de controle deslizante")](slider-switch-segmented-controls-images/image25a.png)

Você pode definir esses valores no código conforme mostrado abaixo, incluindo fiação um manipulador para exibir o valor selecionado em um `UILabel` controle:

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

 [ ![](slider-switch-segmented-controls-images/image27a.png "Controle deslizante personalizado")](slider-switch-segmented-controls-images/image28a.png)

> [!IMPORTANT]
> Atualmente, há um [bug](http://stackoverflow.com/a/19496179) fazendo com que o `ThumbTint` para não processar em tempo de execução, conforme o esperado. Você pode adicionar a seguinte linha de código **antes de** o código acima como uma solução alternativa. [[Fonte](http://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Você pode usar qualquer imagem, como ele será substituído, mas verifique se ele é colocado _na_ diretório de recursos e é chamado em seu código.

<a name="Switch" />

## <a name="switch"></a>Alternar

iOS usa o `UISwitch` um booliano que pode ser representado por um botão de opção em outras plataformas de entrada. O usuário pode manipular o controle movendo o *thumb* entre o **ativar/desativar** posições.

 [ ![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png)

Personalizar a aparência do comutador no **propriedades de preenchimento** do designer, o que permitirá a você controlar o estado padrão, **ativar/desativar tonalidade** cores e uma **on/off imagem**. Isso é ilustrado na imagem abaixo:

 [ ![](slider-switch-segmented-controls-images/image29a.png "Propriedades do comutador")](slider-switch-segmented-controls-images/image29a.png)

As propriedades do comutador também podem ser definidas no código, por exemplo, o código a seguir mostrará um comutador com o valor padrão de `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Controles segmentados

Um controle segmentado é uma maneira organizada para permitir que os usuários interajam com um pequeno número de opções. Está disposto horizontalmente e cada segmento funciona como um botão separado. Ao usar o Designer, o controle segmentado podem ser encontrado em **Ferramentas > controles**e deve ser semelhante a imagem a seguir:

 [ ![](slider-switch-segmented-controls-images/segmentedcontrol.png "Controle segmentado")](slider-switch-segmented-controls-images/segmentedcontrol.png)

Permite que um recurso exclusivo do Designer para cada segmento a ser selecionados individualmente na superfície de design, conforme ilustrado abaixo:

 [ ![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Controle segmentado")](slider-switch-segmented-controls-images/segmentedcontrolselection.png)

Isso permite que o painel de propriedades a serem usadas para controlar com mais precisão as propriedades de cada segmento. Você pode ver as propriedades editáveis na captura de tela abaixo:

 [ ![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Controle segmentado")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png)

Deve-se observar que o estilo de controle segmentado foi preterido no iOS7 e, portanto, ajustar as opções para isso em um aplicativo iOS7 não terá efeito.

## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
- [Controlador de alerta](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
