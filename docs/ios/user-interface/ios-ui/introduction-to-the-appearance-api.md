---
title: API de aparência no Xamarin. iOS
description: o iOS permite aplicar configurações de Propriedade Visual em um nível de classe estática em vez de em objetos individuais para que a alteração se aplique a todas as instâncias desse controle no aplicativo.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 7ba6eca8f74c10254ae93b95725bc73ae100be70
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433852"
---
# <a name="appearance-api-in-xamarinios"></a>API de aparência no Xamarin. iOS

_o iOS permite aplicar configurações de Propriedade Visual em um nível de classe estática em vez de em objetos individuais para que a alteração se aplique a todas as instâncias desse controle no aplicativo._

Essa funcionalidade é exposta no Xamarin. iOS por meio de uma `Appearance` propriedade estática em todos os controles UIKit que dão suporte a ela. A aparência visual (propriedades como cor de tonalidade e imagem de plano de fundo), portanto, pode ser facilmente personalizada para dar a seu aplicativo uma aparência consistente. A API de aparência foi introduzida no iOS 5 e, embora algumas partes dele tenham sido preteridas no iOS 9, ainda é uma boa maneira de obter algum estilo e efeitos de tema nos aplicativos Xamarin. iOS.

## <a name="overview"></a>Visão geral

o iOS permite que você personalize a aparência de muitos controles UIKit para que os controles padrão estejam em conformidade com a identidade visual que você deseja aplicar ao seu aplicativo.

Há duas maneiras diferentes de aplicar uma aparência personalizada:

- **Diretamente em uma instância de controle** – você pode definir a cor da tonalidade, a imagem do plano de fundo e a posição do título (bem como alguns outros atributos) em vários controles, incluindo barras de ferramentas, barras de navegação, botões e controles deslizantes.

- **Definir padrões na propriedade estática de aparência** – atributos personalizáveis para cada controle são expostos por meio da `Appearance` propriedade estática. Todas as personalizações aplicadas a essas propriedades serão usadas como o padrão para qualquer controle desse tipo criado depois que a propriedade for definida.

O aplicativo de exemplo aparência demonstra todos os três métodos, conforme mostrado nestas capturas de tela:

[![O aplicativo de exemplo aparência demonstra todos os três métodos](introduction-to-the-appearance-api-images/appearance01-sml.png)](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partir do iOS 8, o proxy de aparência foi estendido para TraitCollections.
 `AppearanceForTraitCollection` pode ser usado para definir a aparência padrão em uma determinada coleção de características. Você pode ler mais sobre isso no guia [introdução ao storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="setting-appearance-properties"></a>Definindo propriedades de aparência

Na primeira tela, a classe de aparência estática é usada para estilizar os botões e elementos amarelo/laranja como este:

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

Os estilos de elemento verdes são definidos como este, no `ViewDidLoad` método que substitui os valores padrão e a classe estática de *aparência* :

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Usando UIAppearance no Xamarin. Forms

A API de aparência pode ser útil ao [estilizar o aplicativo IOS](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api) em soluções Xamarin. Forms. Algumas linhas na `AppDelegate` classe podem ajudar a implementar um esquema de cores específico sem precisar criar um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

### <a name="custom-themes-and-uiappearance"></a>Temas e UIAppearance personalizados

o iOS permite que muitos atributos visuais dos controles da interface do usuário sejam "com tema" usando as APIs do *UIAppearance* para forçar todas as instâncias de um controle específico a terem a mesma aparência. Isso é exposto como uma propriedade Appearance em muitas classes de controle de interface do usuário, não em instâncias individuais do controle. A definição de uma propriedade de exibição na `Appearance` propriedade estática afeta todos os controles desse tipo em seu aplicativo.

Para entender melhor o conceito, considere um exemplo.

Para alterar um específico `UISegmentedControl` para ter uma tonalidade magenta, referenciamos o controle específico em nossa tela como esta em `ViewDidLoad` :

```csharp
sg1.TintColor = UIColor.Magenta;
```

Como alternativa, defina o valor no painel de propriedades do designer:

[![Tonalidade de Painel de Propriedades](introduction-to-the-appearance-api-images/propertiespadtint.png)](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

A imagem abaixo ilustra que isso define a tonalidade somente no controle chamado ' SG1 '.

[![Definindo a tonalidade de controle individual](introduction-to-the-appearance-api-images/image53.png)](introduction-to-the-appearance-api-images/image53.png#lightbox)

Definir muitos controles dessa maneira seria completamente ineficiente, portanto, podemos definir a `Appearance` propriedade estática na própria classe. Isso é mostrado no código abaixo:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

A imagem abaixo agora ilustra os dois controles segmentados com a aparência definida como magenta:

[![Definindo a tonalidade do controle de aparência](introduction-to-the-appearance-api-images/image54.png)](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` as propriedades devem ser definidas no início do ciclo de vida do aplicativo, como no `FinishedLaunching` evento do AppDelegate ou em um ViewController antes de os controles afetados serem exibidos.

Consulte a [introdução à API de aparência](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) para obter informações mais detalhadas.

## <a name="related-links"></a>Links Relacionados

- [Aparência (exemplo)](/samples/xamarin/ios-samples/appearance)
- [Referência de protocolo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Aparência no Xamarin. Forms](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api)