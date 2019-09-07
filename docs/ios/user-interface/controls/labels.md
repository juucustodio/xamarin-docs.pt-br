---
title: Rótulos no Xamarin. iOS
description: Este documento discute como usar rótulos no Xamarin. iOS. Ele descreve como criar rótulos programaticamente e com o designer do iOS.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: 275acd02e6cece0842344d8e5833c23af1014a0f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768944"
---
# <a name="labels-in-xamarinios"></a>Rótulos no Xamarin. iOS

O `UILabel` controle é usado para exibir texto único e de várias linhas, somente leitura.

## <a name="implementing-a-label"></a>Implementando um rótulo

Um novo rótulo é criado instanciando-se [`UILabel`](xref:UIKit.UILabel)um:

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Rótulos e storyboards

Você também pode adicionar um rótulo à sua interface do usuário ao usar o designer do iOS. Procure **rótulo** na caixa de **ferramentas** e arraste-o para sua exibição:

![Rótulo na caixa de ferramentas](labels-images/image3.png)

As propriedades a seguir podem ser ajustadas no painel de propriedades:

![Painel de propriedades do rótulo](labels-images/image2.png)

- **Contexto de texto** – simples ou atribuído. Texto sem formatação permite que você defina os [atributos de formatação](#Formatting_Text_and_Label) na cadeia de caracteres inteira. Os textos atribuídos permitem que você defina a formatação para diferentes caracteres ou palavras na cadeia de caracteres.
- **Cor, fonte, alinhamento** – atributos de formatação que podem ser aplicados ao rótulo.
- **Linhas** – define o número de linhas que o rótulo pode abranger. Defina como 0 para permitir que o rótulo use quantas linhas forem necessárias.
- **Comportamento** – pode ser definido como habilitado ou realçado. Habilitado é definido por padrão, o texto desabilitado será exibido em uma cor cinza mais clara. Realçado está desabilitado por padrão e permite que o rótulo seja redesenhado com um estado realçado quando ele é selecionado por um usuário.
- **Baselane e quebra de linha** –
  - Da linha determina como o texto será posicionado se os tamanhos de fonte forem diferentes do especificado.
  - Quebras de linha determinam como uma cadeia de caracteres será encapsulada ou truncada se for maior que uma única linha.
- **Redução** automática – determina como o tamanho da fonte será minimizado dentro de um rótulo, se necessário.
- **Realçado, sombra, deslocamento** – permite que você defina o realçado e a cor da sombra e o deslocamento da sombra.

## <a name="truncating-and-wrapping"></a>Truncamento e disposição

Para obter informações sobre como usar as quebras de linha no iOS, consulte a receita [truncar e quebrar texto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) .

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Formatando texto e rótulo

Para formatar a cadeia de caracteres que você usa em um rótulo, você pode definir atributos de formatação na cadeia de caracteres inteira ou pode usar cadeias de caracteres atribuídas. Os exemplos a seguir mostram como implementá-los:

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

Para obter mais informações sobre como estilizar o texto usando `NSAttributedString` , consulte a receita de texto de [estilo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) .

Por padrão, os rótulos `Enabled` têm o conjunto como verdadeiro, mas é possível defini-lo como desabilitado para dar ao usuário uma dica de que um determinado controle está desabilitado:

```csharp
label.Enabled = false;
```

Isso define o rótulo para uma cor cinza-claro, conforme ilustrado na imagem de exemplo a seguir da tela de restrições no iOS:

![Botão desabilitado no iOS](labels-images/image1.png)

Você também pode definir as cores de texto de realce e sombra para o texto do rótulo para efeitos adicionais:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Que exibe o texto como este:

![Realce e conjunto de sombras no texto](labels-images/image4.png)

Para obter mais informações sobre como alterar a fonte de um UILabel, consulte [alterar a](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) receita da fonte.
