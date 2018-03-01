---
title: "Botões"
description: "A classe UIButton é usada para representar vários estilos diferentes de botão nas telas do iOS. Esta seção apresenta as diferentes opções para trabalhar com os botões no iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: ab7a77c44412bd22427e17a696eb43278ff7dd94
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="buttons"></a>Botões

_A classe UIButton é usada para representar vários estilos diferentes de botão nas telas do iOS. Esta seção apresenta as diferentes opções para trabalhar com os botões no iOS._

O `UIButton`classe representa um controle de botão no iOS. 

Botão Propriedades podem ser editadas no `Properties Pad` do designer de iOS:


![](buttons-images/properties.png "O painel de propriedades do designer de iOS")

## <a name="creating-a-button"></a>Criar um botão

Um UIButton pode ser criado por meio de apenas algumas linhas de código.

Primeiro, criar uma instância de um novo botão e especifique o tipo de botão que você precisa:

```csharp
UIButton myButton = new UIButton(UIButtonType.System);
```

O UIButtonType deve ser especificado como um dos seguintes:

- **Sistema** -este é o tipo de botão padrão usado pelo iOS e é o tipo que você usará com mais frequência.
- **DetailDisclosure** -apresenta um tipo de "Desativar" de botão usada para ocultar ou mostrar informações detalhadas.
- **InfoDark** -um escuro detalhadas informações botão exibido um "i" em um círculo.
- **InfoLight** -clara detalhadas informações botão exibido um "i" em um círculo.
- **AddContact** -exibir o botão como um botão Adicionar contato.
- **Personalizar** -permite que você personalize várias características do botão.

Para obter mais informações sobre tipos de botão podem ser encontradas na [botão tipos](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/create_different_types_of_buttons/) receita.

Em seguida, defina o tamanho na tela e o local do botão. Exemplo:

```csharp
myButton.Frame = new CGRect (25, 25, 300, 150);
```

Para alterar o texto em um botão, use o `SetTitle` propriedade do botão, o que exige que você defina uma cadeia de caracteres de texto e um `UIControlStyle`. Por exemplo:

```csharp
myButton.SetTitle("Hello, World!", UIControlState.Normal);
```

Definir propriedades diferentes para cada estado permite comunicar-se para obter mais informações para o usuário (por exemplo. Verifique a cor do texto cinza para o estado desabilitado). Você pode alternar entre cada estado usando o Designer do iOS, ou você pode fazer isso programaticamente. Para obter mais informações sobre o texto do botão de configuração e estado, consulte o [definir o texto do botão](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/set_button_text/) receita.

## <a name="dealing-with-user-interactions"></a>Lidando com interações do usuário


Botões não são muito úteis, a menos que eles fazer algo quando clicado! 

No iOS eventos nos botões quase sempre são eventos de toque, como o uso interage com o botão na tela tocando-lo. Uma lista de todos os eventos UIControl possíveis estão listados [aqui](https://developer.apple.com/documentation/uikit/uicontrolevents), mas o evento mais comumente usado no iOS é `TouchUpInside`. Em seguida, você pode criar um manipulador de eventos para fazer algo quando o botão foi pressionado:


```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

### <a name="adding-events-in-the-ios-designer"></a>Adicionando eventos no Designer de iOS
 
Use a guia de eventos no painel de propriedade para adicionar eventos para controles.

Selecione o evento e digite o nome de um novo manipulador de eventos ou selecione um na lista. Isso criará um novo método parcial em sua classe de controlador de exibição.

![Guia de eventos](buttons-images/image1.png)

## <a name="styling-a-button"></a>Um botão de estilo

UIButtons diferentes da maioria dos UIKit controla em que eles têm um estado para que você simplesmente não pode alterar o título, você precisa alterá-la para cada `UIControlState`. Definir a cor do título e a cor de sombra é feita de maneira semelhante:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Além disso, você pode usar o texto atribuído como título do botão. Por exemplo:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botão personalizado


Quando você define o `Custom` tipo de botão, o objeto não tem nenhum processamento padrão. Você pode configurar a aparência do botão, definindo uma imagem para os diferentes estados. Por exemplo, o código a seguir demonstra como adicionar imagens diferentes para o `Normal`, `Highlighted` e `Selected` estados:


```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```


Dependendo se o usuário estiver tocando no botão ou não, ele será renderizado como uma das seguintes imagens (`Normal`, `Highlighted` e `Selected` estados respectivamente):


![](buttons-images/image22.png "UIButton estado Normal")
![](buttons-images/image23.png "UIButton estado realçado")
![](buttons-images/image24.png "UIButton estado selecionado")

Para obter mais informações sobre como trabalhar com botões personalizados, consulte o [usar uma imagem de um botão](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/use_an_image_for_a_button/).


## <a name="related-links"></a>Links relacionados

- [Pasta de trabalho UIButton](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
