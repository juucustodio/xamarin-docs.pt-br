---
title: Rótulos no xamarin. IOS
description: Este documento discute como usar os rótulos no xamarin. IOS. Ele descreve como criar etiquetas de forma programática e com o Designer do iOS.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 7c52ce9fcdd290958a53af7fa91ef93a2caff7b5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108958"
---
# <a name="labels-in-xamarinios"></a>Rótulos no xamarin. IOS

O `UILabel` controle é usado para a exibição única e multilinha, ler somente texto. 

## <a name="implementing-a-label"></a>Implementando um rótulo

Criar um novo rótulo é uma instância de um [ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Rótulos e Storyboards

Você também pode adicionar um rótulo para a interface do usuário ao usar o Designer do iOS. Pesquise **etiqueta** na **caixa de ferramentas** e arraste-o para o modo de exibição:

![Na caixa de ferramentas de rótulo](labels-images/image3.png)

As propriedades a seguir podem ser ajustadas no painel de propriedades:

![Painel de propriedades do rótulo](labels-images/image2.png)

- **Contexto de texto** – sem formatação ou atribuído. Texto sem formatação, permite que você defina as [atributos de formatação](#Formatting_Text_and_Label) na cadeia de caracteres inteira. Textos atribuídos permite que você defina a formatação para diferentes caracteres ou palavras na cadeia de caracteres.
- **Cor, fonte, alinhamento** – atributos de formatação que podem ser aplicados ao rótulo.
- **Linhas** – define o número de linhas que pode se estender o rótulo. Defina isso como 0 para permitir que o rótulo usar tantas linhas conforme necessário.
- **Comportamento** – pode ser definido como ativado ou realçada. Habilitado é definido por padrão, o texto desabilitado será exibido em uma cor mais clara. Realçado é desabilitado por padrão e permite que o rótulo a ser redesenhado com um estado realçado quando ele é selecionado por um usuário.
- **Quebra de linha e de Baselane** – 
    - Basline determina como o texto será posicionado se os tamanhos de fonte é diferente do especificado.
    - As quebras de linha determinam como uma cadeia de caracteres será encapsulada ou truncada se ele tiver mais de uma única linha.
- **Redução automática** – determina como a fonte em tamanho será minimizada dentro de um rótulo, se necessário.
- **Realçado, sombra, deslocamento** – permite que você defina a cor de sombra e realçado e o deslocamento de sombra.

## <a name="truncating-and-wrapping"></a>Truncando e encapsulamento

Para obter informações sobre como usar a linha de quebra no iOS, consulte a [truncar e quebrar o texto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) receita.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Formatando texto e rótulo

Para formatar a cadeia de caracteres que você usa em um rótulo você pode definir atributos em toda a cadeia de formatação ou você pode usar cadeias de caracteres atribuídas. Os exemplos a seguir mostram como implementá-los:

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

Para obter mais informações sobre como usar o estilo de texto `NSAttributedString` consulte o [estilo do texto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) receita.

Por padrão, rótulos de tem o `Enabled` definido como true, mas é possível defini-lo como desativado para dar ao usuário uma dica de que um determinado controle está desabilitado:

```csharp
label.Enabled = false;
```

Isso define o rótulo para uma cor cinza clara, conforme ilustrado na imagem de exemplo da tela de restrições no iOS:

![Botão desabilitado no iOS](labels-images/image1.png)

Você também pode definir as cores de realce e sombra do texto para o texto do rótulo para efeitos adicionais:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Que exibe o texto como esta:

![Realce e sombra definidas em texto](labels-images/image4.png)

Para obter mais informações sobre como alterar a fonte de um UILabel, consulte o [alterar a fonte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) receita.





