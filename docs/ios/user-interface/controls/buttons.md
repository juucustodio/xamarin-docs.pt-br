---
title: Botões no xamarin. IOS
description: A classe UIButton é usada para representar vários estilos diferentes de um botão em telas de iOS. Este guia apresenta as diferentes opções para trabalhar com os botões no iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2018
ms.openlocfilehash: 32f6330ad2fddc2e8386d6e574918a011f3bebad
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38985998"
---
# <a name="buttons-in-xamarinios"></a>Botões no xamarin. IOS

No iOS, o `UIButton` classe representa um controle de botão.

Propriedades de um botão podem ser modificadas por meio de programação ou com o **painel de propriedades** do Designer do iOS:

![O painel de propriedades do Designer do iOS](buttons-images/properties.png "o painel de propriedades do Designer do iOS")

## <a name="creating-a-button-programmatically"></a>Criando um botão de forma programática

Um `UIButton` podem ser criados com apenas algumas linhas de código.

- Criar uma instância de um botão e especificar seu tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  Tipo do botão é especificado por um `UIButtonType`:

  - `UIButtonType.System` -Um botão de finalidade geral
  - `UIButtonType.DetailDisclosure` -Indica a disponibilidade de informações detalhadas, geralmente sobre um item específico em uma tabela
  - `UIButtonType.InfoDark` -Indica a disponibilidade das informações de configuração; cores escuro
  - `UIButtonType.InfoLight` -Indica a disponibilidade das informações de configuração; claros
  - `UIButtonType..AddContact` -Indica que um contato pode ser adicionado
  - `UIButtonType.Custom` -Botão personalizável

  Para obter mais informações sobre os tipos diferentes de botão, dê uma olhada:
  
  - O [tipos de botão personalizado](#custom-button-types) seção deste documento
  - O [tipos de botão](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons) receita
  - Da Apple [diretrizes de Interface humana do iOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/).

- Defina o tamanho e a posição do botão:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Defina o texto do botão. Use o `SetTitle` método, o que exige que o texto e um `UIControlState` valor:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Para obter mais informações sobre como definir o estilo de um botão e definir seu texto, consulte:

  - O [definindo o estilo de um botão](#styling-a-button) seção deste documento
  - O [definir o texto do botão](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) receita.

## <a name="handling-a-button-tap"></a>Tratamento de um toque de botão

Para responder a um toque de botão, fornecer um manipulador para o botão `TouchUpInside` eventos:

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` não é o evento do botão somente está disponível. `UIButton` é uma classe filha da `UIControl`, que define [muitos eventos diferentes](https://developer.xamarin.com/api/type/UIKit.UIControlEvent/).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Usando o Designer do iOS para especificar manipuladores de eventos do botão

Use o **eventos** guia da **painel de propriedades** para especificar os manipuladores de eventos para um botão de vários eventos.

Para o evento apropriado, digite o nome de um novo manipulador de eventos ou selecione um na lista. Isso criará um manipulador de eventos no código do controlador de exibição do botão.

![Guia de eventos do painel de propriedades](buttons-images/image1.png "guia eventos de painel de propriedades")

## <a name="styling-a-button"></a>Definir o estilo de um botão

`UIButton` controles podem existir em um número de estados diferentes, cada uma especificada por um `UIControlState` valor – `Normal`, `Disabled`, `Focused`, `Highlighted`, etc. Cada estado pode ser fornecido um estilo exclusivo, especificado por meio de programação ou com o Designer do iOS.

> [!NOTE]
> Para obter uma lista completa de todos os `UIControlState` valores, dê uma olhada a [ `UIKit.UIControlState enumeration` ](https://developer.xamarin.com/api/type/UIKit.UIControlState/) documentação.

Por exemplo, para definir a cor do título e a cor da sombra para `UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

O código a seguir define o título do botão em uma cadeia de caracteres (estilizado) atribuído para `UIControlState.Normal` e `UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botão personalizado

Botões com uma `UIButtonType` de `Custom` não têm nenhum estilo padrão. No entanto, é possível configurar a aparência do botão, definindo uma imagem para seus estados diferentes:

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

Dependendo se o usuário toca no botão ou não, ele será renderizado como uma das seguintes imagens (`UIControlState.Normal`, `UIControlState.Highlighted` e `UIControlState.Selected` afirma, respectivamente):

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted") 
 ![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

Para obter mais informações sobre como trabalhar com botões personalizados, consulte o [usar uma imagem para um botão](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) receita.

## <a name="related-links"></a>Links relacionados

- [Pasta de trabalho UIButton](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
