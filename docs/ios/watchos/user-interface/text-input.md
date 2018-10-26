---
title: Trabalhando com entrada de texto no Xamarin do watchOS
description: Este documento descreve a entrada de texto do watchOS no Xamarin. Ele aborda o PresentTextInputController método, escrevendo, texto sem formatação, emojis e ditado.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 2092b12254008936f2c5b6a7d9dd610ff751e802
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122355"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Trabalhando com entrada de texto no Xamarin do watchOS

O Apple Watch não oferece um teclado para os usuários a entrada de texto, no entanto, ele oferece suporte a algumas alternativas de inspeção amigável:

- A seleção de uma lista predefinida de opções de texto,
- Siri ditado,
- Escolhendo um emoji
- Scribble do reconhecimento de manuscrito de letra por letra (introduzido no watchOS 3).

O simulador não oferece suporte a ditado, mas você ainda pode testar a entrada de texto outras opções do controlador, como rabisco, conforme mostrado aqui:

![](text-input-images/textinput-sml.png "Testando a opção de rabisco")

Para aceitar a entrada de texto em um aplicativo de inspeção:

1. Crie uma matriz de cadeia de caracteres de opções predefinidas.
2. Chame `PresentTextInputController` com a matriz, se deseja permitir emoji ou não e um `Action` que é chamado quando o usuário for concluído.
3. Na ação de conclusão, teste o resultado de entrada e tomar as devidas providências no aplicativo (possivelmente definindo o valor de texto do rótulo para um).

O trecho de código a seguir apresenta três opções predefinidas para o usuário:

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

O `WKTextInputMode` enumeração possui três valores:

- Sem formatação
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Sem formatação

Quando o modo simples é definido, o usuário pode escolher:

- Ditado,
- Rabisco, ou
- em uma lista predefinida que fornece o aplicativo.

[![](text-input-images/plain-scribble-sml.png "Ditado, rabisco, ou de uma lista predefinida que fornece o aplicativo")](text-input-images/plain-scribble.png#lightbox)

O resultado sempre é retornado como um `NSObject` que pode ser convertido em um `string`.

## <a name="emoji"></a>Emoji

Há dois tipos de emoji:

- Normal emoji de Unicode
- Imagens animadas

Quando o usuário escolhe um emoji Unicode, ele é retornado como uma cadeia de caracteres.

Se uma imagem animada emoji for selecionado o `result` na conclusão manipulador conterá um `NSData` objeto que contém o emoji `UIImage`.

## <a name="accepting-dictation-only"></a>Aceitando o ditado somente

Para levar o usuário diretamente para a tela de ditado sem mostrar qualquer sugestão (ou a opção de rabisco):

- passar uma matriz vazia para a lista de sugestões, e
- Definir `WatchKit.WKTextInputMode.Plain`.

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

Quando o usuário está falando, a tela de observação exibe a tela a seguir que inclui o texto, que é entendido (por exemplo "Este é um teste"):

![](text-input-images/dictation.png "Quando o usuário está falando, a tela de observação exibe o texto como ele é compreendido")

Quando eles pressionam a **feito** botão, o texto será retornado.



## <a name="related-links"></a>Links relacionados

- [Do Apple documento de texto e rótulos](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
