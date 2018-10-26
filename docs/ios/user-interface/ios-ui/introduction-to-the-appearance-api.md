---
title: API de aparência no xamarin. IOS
description: iOS permite aplicar configurações de propriedade visual em um nível de classe estática em vez de em objetos individuais, para que a alteração se aplica a todas as instâncias desse controle no aplicativo.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 0dd9832a2e4dd0803f92d6e3923fe178252211f4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103563"
---
# <a name="appearance-api-in-xamarinios"></a>API de aparência no xamarin. IOS

_iOS permite aplicar configurações de propriedade visual em um nível de classe estática em vez de em objetos individuais, para que a alteração se aplica a todas as instâncias desse controle no aplicativo._

Essa funcionalidade é exposta no xamarin. IOS por meio de um estático `Appearance` propriedade em todos os controles UIKit que dão suporte a ele. A aparência visual (propriedades como como imagem de tonalidade cor e tela de fundo), portanto, pode ser facilmente personalizada para dar uma aparência consistente de seu aplicativo. A API de aparência foi introduzida no iOS 5 e, embora algumas partes dele foram substituídas no iOS 9 ainda é uma boa maneira de obter alguns estilos e efeitos de temas em aplicativos xamarin. IOS.

## <a name="overview"></a>Visão geral

iOS permite que você personalize a aparência dos muitos controles UIKit para fazer com que os controles padrão estão em conformidade com a marca que você deseja aplicar ao seu aplicativo.

Há duas maneiras diferentes de aplicar uma aparência personalizada:

- **Diretamente em uma instância de controle** – você pode definir a cor da tonalidade, imagem de plano de fundo e posição do título (bem como alguns outros atributos) em vários controles, incluindo as barras de ferramentas, barras de navegação, botões e controles deslizantes.

- **Definir padrões sobre a propriedade estática de aparência** – atributos personalizáveis para cada controle são expostos por meio de `Appearance` propriedade estática. As personalizações que se aplicam a essas propriedades serão usadas como padrão para qualquer controle desse tipo criado depois que a propriedade é definida.

O aplicativo de exemplo da aparência demonstra todos os três métodos, conforme mostrado nessas capturas de tela:

 [![](introduction-to-the-appearance-api-images/appearance01.png "O aplicativo de exemplo da aparência demonstra todos os três métodos")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partir do iOS 8, o proxy de aparência foi estendido para TraitCollections.
 `AppearanceForTraitCollection` pode ser usado para definir a aparência padrão em uma coleção de característica específica. Você pode ler mais sobre isso na [Introdução ao Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guia.


## <a name="setting-appearance-properties"></a>Definindo propriedades de aparência

Na primeira tela, a classe estática de aparência é usada para estilizar os botões e amarelo/laranja elementos como este:

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

Os estilos do elemento verdes são definidos como este, além de `ViewDidLoad` método que substitui os valores padrão e o *aparência* classe estática:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Usando UIAppearance no xamarin. Forms

A API de aparência pode ser útil quando [definindo o estilo de aplicativo do iOS](~/xamarin-forms/platform/ios/theme.md#uiappearance) em soluções xamarin. Forms. Algumas linhas na `AppDelegate` classe pode ajudar a implementar um esquema de cores específica sem precisar criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).


### <a name="custom-themes-and-uiappearance"></a>UIAppearance e temas personalizados

iOS permite que muitos atributos visuais do usuário controles de interface "tema" usando o *UIAppearance* APIs para forçar todas as instâncias de um controle específico para ter a mesma aparência. Isso é exposto como uma propriedade de aparência em muitas classes de controle de interface de usuário, não em instâncias individuais do controle. Definir uma propriedade de exibição em estático `Appearance` propriedade afeta todos os controles desse tipo em seu aplicativo.

Para entender melhor o conceito, considere um exemplo.

Para alterar um determinado `UISegmentedControl` para ter uma tonalidade Magenta, podemos fazer referência o controle específico em nossa tela como esta na `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

Como alternativa, defina o valor no painel de propriedades do Designer: 

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Tom do painel de propriedades")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

A imagem a seguir ilustra que define o tom no controle denominado 'sg1'.

 [![](introduction-to-the-appearance-api-images/image53.png "Definindo o tom de controle individual")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Para definir muitos controles dessa forma seria completamente ineficiente, portanto, em vez disso, definimos estático `Appearance` propriedade na classe em si. Isso é mostrado no código a seguir:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

Agora, a imagem a seguir ilustra ambos os controles segmentados com a aparência definida como Magenta:

 [![](introduction-to-the-appearance-api-images/image54.png "Definindo o tom de controle de aparência")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` propriedades devem ser definidas no início do ciclo de vida do aplicativo, como no AppDelegate `FinishedLaunching` evento, ou em um ViewController antes que os controles afetados são exibidos.


Consulte a [Introdução à API do aparência](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) para obter mais informações.


## <a name="related-links"></a>Links relacionados

- [Aparência (amostra)](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [Referência de protocolo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Aparência no xamarin. Forms](~/xamarin-forms/platform/ios/theme.md#uiappearance)
