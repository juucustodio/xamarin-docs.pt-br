---
title: "Aparência de API"
description: "iOS permite aplicar as configurações de propriedade visual em um nível de classe estática em vez de em objetos individuais para que a alteração se aplica a todas as instâncias desse controle no aplicativo."
ms.topic: article
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 6d2a454665691c028fe8307940a5662a98ab9c98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="appearance-api"></a>Aparência de API

_iOS permite aplicar as configurações de propriedade visual em um nível de classe estática em vez de em objetos individuais para que a alteração se aplica a todas as instâncias desse controle no aplicativo._

Essa funcionalidade é exposta no xamarin por meio de um estático `Appearance` propriedade em todos os controles de UIKit que dão suporte a ele. A aparência visual (propriedades como como imagem tom de cor e o plano de fundo), portanto, pode ser facilmente personalizada para dar uma aparência consistente de seu aplicativo. A API de aparência foi introduzida no iOS 5 e enquanto algumas partes dele foram substituídos no iOS 9 ainda é uma boa maneira de obter alguns estilos e efeitos de temas em aplicativos xamarin.

## <a name="overview"></a>Visão geral

iOS permite que personalizar a aparência de vários controles de UIKit para fazer com que os controles padrão está de acordo com a marca que você deseja aplicar ao seu aplicativo.

Há duas maneiras de aplicar uma aparência personalizada:

- **Diretamente em uma instância do controle** – você pode definir a cor do matiz, imagem de plano de fundo e posição do título (bem como alguns outros atributos) em vários controles, incluindo as barras de ferramentas, barras de navegação, botões e controles deslizantes.

- **Definir padrões em que a propriedade estática de aparência** – atributos personalizáveis para cada controle são expostos por meio de `Appearance` propriedade estática. As personalizações que se aplicam a essas propriedades serão usadas como padrão para qualquer controle desse tipo criado depois que a propriedade é definida.

O aplicativo de exemplo da aparência demonstra todos os três métodos, conforme mostrado nessas capturas de tela:

 [ ![](introduction-to-the-appearance-api-images/appearance01.png "O aplicativo de exemplo da aparência demonstra todos os três métodos")](introduction-to-the-appearance-api-images/appearance01.png)

A partir do iOS 8, o proxy de aparência foi estendido para TraitCollections.
 `AppearanceForTraitCollection` pode ser usado para definir a aparência padrão em uma coleção de característica específica. Você pode ler mais sobre isso no [Introdução a Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guia.


## <a name="setting-appearance-properties"></a>Definindo propriedades de aparência

Na primeira tela, a classe estática de aparência é usada para definir o estilo os botões e os elementos de amarelo/laranja assim:

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

Os estilos de elemento verde são definidos como este, além de `ViewDidLoad` método que substitui os valores padrão e o *aparência* classe estática:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Usando UIAppearance em xamarin. Forms

A API de aparência pode ser útil quando [estilo o aplicativo iOS](~/xamarin-forms/platform/ios/theme.md#uiappearance) em soluções xamarin. Forms. Algumas linhas no `AppDelegate` pode ajudar a classe para implementar um esquema de cores específicas sem a necessidade de criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).


### <a name="custom-themes-and-uiappearance"></a>UIAppearance e temas personalizados

iOS permite que muitos atributos visual do usuário controles de interface ser "tema" com o *UIAppearance* APIs para forçar a todas as instâncias de um controle específico para ter a mesma aparência. Isso é exposto como uma propriedade de aparência em várias classes de controle de interface de usuário, não em instâncias individuais do controle. Definir uma propriedade de vídeo em estático `Appearance` propriedade afeta todos os controles desse tipo em seu aplicativo.

Para entender melhor o conceito, considere um exemplo.

Para alterar um determinado `UISegmentedControl` para ter um tom Magenta, podemos fazer referência o controle específico em nossa tela como esta no `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

Como alternativa, defina o valor no painel de propriedades do Designer: 

[ ![](introduction-to-the-appearance-api-images/propertiespadtint.png "Tonalidade de preenchimento de propriedades")](introduction-to-the-appearance-api-images/propertiespadtint.png)

A imagem abaixo ilustra que define o tom no controle chamado 'sg1'.

 [ ![](introduction-to-the-appearance-api-images/image53.png "Definindo o tom de controle individual")](introduction-to-the-appearance-api-images/image53.png)

Para definir vários controles dessa maneira são completamente ineficiente, portanto, em vez disso, podemos definir estático `Appearance` propriedade na classe em si. Isso é mostrado no código a seguir:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

A imagem a seguir ilustra agora ambos os controles segmentados com a aparência definida como Magenta:

 [ ![](introduction-to-the-appearance-api-images/image54.png "Definindo o tom de controle de aparência")](introduction-to-the-appearance-api-images/image54.png)

`Appearance` propriedades devem ser definidas no início do ciclo de vida do aplicativo, como no AppDelegate `FinishedLaunching` evento, ou em um ViewController antes que os controles afetados são exibidos.


Consulte o [Introdução à API aparência](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) para obter mais informações.


## <a name="related-links"></a>Links relacionados

- [Aparência (exemplo)](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [Referência de protocolo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Aparência no xamarin. Forms](~/xamarin-forms/platform/ios/theme.md#uiappearance)
