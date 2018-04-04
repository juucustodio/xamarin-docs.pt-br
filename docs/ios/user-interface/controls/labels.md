---
title: Rótulos
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 8a5b2c12cfbca18280a8044d3d8c599d848de91d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="labels"></a>Rótulos

O `UILabel` controle é usado para exibir único e várias linhas, de leitura somente texto. 

## <a name="implementing-a-label"></a>Implementando um rótulo

É criado um novo rótulo instanciando um [ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Rótulos e Storyboards

Você também pode adicionar um rótulo para a interface do usuário ao usar o Designer do iOS. Procurar **rótulo** no **caixa de ferramentas** e arraste-o para o modo de exibição:

![Rótulo na caixa de ferramentas](labels-images/image3.png)

As seguintes propriedades podem ser ajustadas no painel de propriedades:

![Painel de propriedades do rótulo](labels-images/image2.png)

- **Contexto de texto** – simples ou atribuído. Texto simples permite que você defina o [atributos de formatação](#Formatting_Text_and_Label) na cadeia de caracteres inteira. Textos atribuídos permite que você defina a formatação para diferentes caracteres ou palavras na cadeia de caracteres.
- **Alinhamento de cor, fonte,** – atributos de formatação que pode ser aplicada ao rótulo.
- **Linhas** – define o número de linhas que o rótulo pode abranger. Defina como 0 para permitir que o rótulo a ser usado como número de linhas conforme necessário.
- **Comportamento** – pode ser definido como ativado ou realçada. Habilitado é definido por padrão, texto desabilitado será exibido em uma cor mais clara. Realçada é desabilitada por padrão e permite que o rótulo a ser redesenhado com um estado realçado quando ele é selecionado por um usuário.
- **Quebra de linha e de Baselane** – 
    - Basline determina como o texto será posicionado se os tamanhos de fonte é diferente do especificado.
    - As quebras de linha determinam como uma cadeia de caracteres será encapsulada ou truncada se tiver mais de uma única linha.
- **Redução automática** – determina como a tamanho de fonte será minimizada dentro de um rótulo, se necessário.
- **Realçado, sombra, deslocamento** – permite que você defina a cor de sombra e realçado e deslocamento de sombra.

## <a name="truncating-and-wrapping"></a>Truncando e encapsulamento

Para obter informações sobre como usar a linha de quebra no iOS, consulte o [truncar e quebrar o texto](https://developer.xamarin.com/recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text/) receita.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Rótulo e formatação de texto

Para formatar a cadeia de caracteres que você usa em um rótulo você pode definir atributos em toda a cadeia de caracteres de formatação ou você pode usar cadeias de caracteres atribuídas. Os exemplos a seguir mostram como implementá-los:

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

Para obter mais informações sobre como usar o estilo de texto `NSAttributedString` consulte o [texto de estilo](https://developer.xamarin.com/recipes/ios/standard_controls/text_field/style_text/) receita.

Por padrão, rótulos têm o `Enabled` definido como true, mas é possível defini-lo como desativado para fornecer ao usuário uma dica de que um determinado controle está desabilitado:

```csharp
label.Enabled = false;
```

Isso define o rótulo para uma cor cinza clara, conforme ilustrado na imagem de exemplo de tela de restrições do iOS:

![Botão desabilitado no iOS](labels-images/image1.png)

Você também pode definir as cores de realce e sombra do texto para o texto do rótulo de efeitos adicionais:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Que exibe o texto como este:

![Realce e sombra definidas em texto](labels-images/image4.png)

Para obter mais informações sobre como alterar a fonte de um UILabel, consulte o [alterar a fonte](https://developer.xamarin.com/recipes/ios/standard_controls/labels/change_the_font/) receita.





