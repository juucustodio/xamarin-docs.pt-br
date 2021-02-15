---
title: Botões no Xamarin. iOS
description: A classe UIButton é usada para representar vários estilos diferentes de botão em telas do iOS. Este guia apresenta as diferentes opções para trabalhar com botões no iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2018
ms.openlocfilehash: d763d4433ef0979343de059c5113314f553142fa
ms.sourcegitcommit: 01ccefd54c0ced724784dbe1aec9ecfc9b00e633
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630212"
---
# <a name="buttons-in-xamarinios"></a>Botões no Xamarin. iOS

No iOS, a `UIButton` classe representa um controle de botão.

As propriedades de um botão podem ser modificadas programaticamente ou com a **janela Propriedades ou o painel** do designer do IOS:

![O Painel de Propriedades do designer do iOS](buttons-images/properties.png "O Painel de Propriedades do designer do iOS")

## <a name="creating-a-button-programmatically"></a>Criando um botão programaticamente

Um `UIButton` pode ser criado com apenas algumas linhas de código.

- Crie uma instância de um botão e especifique seu tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  O tipo do botão é especificado por uma `UIButtonType` Propriedade:

  - `UIButtonType.System` -Um botão de finalidade geral
  - `UIButtonType.DetailDisclosure` -Indica a disponibilidade de informações detalhadas, geralmente sobre um item específico em uma tabela
  - `UIButtonType.InfoDark` -Indica a disponibilidade das informações de configuração; cor escura
  - `UIButtonType.InfoLight` -Indica a disponibilidade das informações de configuração; cor clara
  - `UIButtonType..AddContact` -Indica que um contato pode ser adicionado
  - `UIButtonType.Custom` -Botão personalizável

  Para obter mais informações sobre os diferentes tipos de botão, dê uma olhada em:
  
  - A seção [tipos de botão personalizados](#custom-button-types) deste documento
  - A receita dos [tipos de botão](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)
  - Diretrizes de [interface humana do IOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)da Apple.

- Defina o tamanho e a posição do botão:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Defina o texto do botão. Use o `SetTitle` método, que exige o texto e um `UIControlState` valor para o estado do botão:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```
  
  Os tipos de estado do botão estão listados abaixo:
  
  - `UIControlState.Normal`
  - `UIControlState.Highlighted`
  - `UIControlState.Disabled`
  - `UIControlState.Selected`
  - `UIControlState.Focused`
  - `UIControlState.Application`
  - `UIControlState.Reserved`
  
  Para obter mais informações sobre como estilizar um botão e definir seu texto, consulte:

  - O [estilo de uma](#styling-a-button) seção de botão deste documento
  - A receita de [texto do botão Set](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) .

## <a name="handling-a-button-tap"></a>Lidando com um toque de botão

Para responder a um toque de botão, forneça um manipulador para o evento do botão `TouchUpInside` :

```csharp
myButton.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` Não é o único evento de botão disponível. `UIButton` é uma classe filho de `UIControl` , que define [muitos eventos diferentes](xref:UIKit.UIControlEvent).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Usando o designer do iOS para especificar manipuladores de eventos de botão

Use a guia **eventos** do **painel de propriedades** para especificar manipuladores de eventos para vários eventos de um botão.

Para o evento apropriado, digite o nome de um novo manipulador de eventos ou selecione um na lista. Isso criará um manipulador de eventos no código para o controlador de exibição do botão.

![Guia eventos do Painel de Propriedades](buttons-images/image1.png "Guia eventos do Painel de Propriedades")

## <a name="styling-a-button"></a>Estilizando um botão

`UIButton` os controles podem existir em vários Estados diferentes, cada um especificado por um `UIControlState` valor – `Normal` , `Disabled` , `Focused` , `Highlighted` etc. Cada Estado pode receber um estilo exclusivo, especificado programaticamente ou com o designer do iOS.

> [!NOTE]
> Para obter uma lista completa de todos os `UIControlState` valores, dê uma olhada no [`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> online.

Por exemplo, para definir a cor do título e a cor da sombra para `UIControlState.Normal` :

```csharp
myButton.SetTitleColor(UIColor.White, UIControlState.Normal);
myButton.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

O código a seguir define o título do botão para uma cadeia de caracteres (estilizada) atribuída para `UIControlState.Normal` e `UIControlState.Highlighted` :

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botão personalizados

Os botões com um `UIButtonType` de `Custom` não têm estilos padrão. No entanto, é possível configurar a aparência do botão definindo uma imagem para seus Estados diferentes:

```csharp
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

Dependendo se o usuário estiver tocando no botão ou não, ele será renderizado como uma das seguintes imagens ( `UIControlState.Normal` `UIControlState.Highlighted` e `UIControlState.Selected` Estados, respectivamente):

![UIControlState. normal](buttons-images/image22.png "UIControlState. normal") 
 ![UIControlState. realçado](buttons-images/image23.png "UIControlState. realçado") 
 ![UIControlState. Selected](buttons-images/image24.png "UIControlState. Selected")

Para obter mais informações sobre como trabalhar com botões personalizados, consulte a receita [usar uma imagem para um botão](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) .
