---
title: Acessibilidade no iOS
description: Este documento descreve a acessibilidade no iOS, abordando diversas propriedades e recursos que podem ser usados para tornar seu aplicativo utilizável por tantos usuários quanto possível.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/18/2016
ms.openlocfilehash: aa3e15797ae1dac621ea8a78345044be1387ebaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108893"
---
# <a name="accessibility-on-ios"></a>Acessibilidade no iOS

Esta página descreve como usar as APIs de acessibilidade do iOS para criar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte a [Android acessibilidade](~/android/app-fundamentals/accessibility.md) e [acessibilidade dos X](~/mac/app-fundamentals/accessibility.md) páginas para outras APIs da plataforma.

## <a name="describing-ui-elements"></a>Que descreve os elementos de interface do usuário

iOS fornece o `AccessibilityLabel` e `AccessibilityHint` leitor para que os controles mais acessível de tela de propriedades para os desenvolvedores adicionar texto descritivo que pode ser usado pelo VoiceOver. Controles também podem ser marcados com um ou mais características que fornecem contexto adicional nos modos acessíveis.

Alguns controles talvez não precise ser acessível (por exemplo, um rótulo em uma entrada de texto ou uma imagem que seja puramente decorativa) – o `IsAccessibilityElement` é fornecido para desabilitar a acessibilidade nesses casos.

**Designer de interface do usuário**

O **painel de propriedades** contém uma seção de acessibilidade que permite que essas configurações sejam editados quando um controle é selecionado no Designer de interface do usuário do iOS:

![](accessibility-images/ios-designer-sml.png "Configurações de acessibilidade")

**C#**

Essas propriedades também podem ser definidas diretamente no código:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>O que é AccessibilityIdentifier?

O `AccessibilityIdentifier` é usado para definir uma chave exclusiva que pode ser usada para se referir a elementos de interface do usuário por meio da API de automação da IU.

O valor de `AccessibilityIdentifier` nunca é falada nem exibidos ao usuário.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

O `UIAccessibility.PostNotification` método permite que os eventos a serem gerados ao usuário fora de interação direta (por exemplo, quando eles interagem com um controle específico).

### <a name="announcement"></a>Comunicado

Um anúncio pode ser enviado de código para informar ao usuário que algum estado alterado (como uma operação em segundo plano foi concluída). Isso pode ser acompanhado por uma indicação visual na interface do usuário:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

O `LayoutChanged` comunicado é usado quando o layout da tela:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>Acessibilidade e localização

Propriedades de acessibilidade, como o rótulo e dica de podem ser localizados apenas como outro texto na interface do usuário.

**MainStoryboard.strings**

Se a interface do usuário é disposta em um storyboard, você pode fornecer traduções para propriedades de acessibilidade da mesma forma que as outras propriedades. No exemplo a seguir, uma `UITextField` tem uma **ID de localização** de `Pqa-aa-ury` e duas propriedades de acessibilidade, que está sendo definidas em espanhol:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Esse arquivo seria colocado na **es.lproj** diretório para o conteúdo de espanhol.

**Localizable.strings**

Como alternativa, as traduções podem ser adicionadas para o **Localizable.strings** arquivo no (por exemplo, o diretório de conteúdo localizado **es.lproj** para espanhol):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Essas traduções podem ser usadas em C# por meio de `LocalizedString` método:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Consulte a [guia de localização do iOS](~/ios/app-fundamentals/localization/index.md) para obter mais detalhes sobre a localização de conteúdo.

<a name="testing" />

## <a name="testing-accessibility"></a>Teste de acessibilidade

VoiceOver está habilitada no **as configurações** aplicativo navegando para **geral > Acessibilidade > VoiceOver**:

![](accessibility-images/settings-sml.png "Definir a taxa de fala")

O **acessibilidade** tela também fornece as configurações de zoom, tamanho do texto, opções de cor e contraste, as configurações de fala e outras opções de configuração.

Siga estas [instruções VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) para testar a acessibilidade em dispositivos iOS.


## <a name="simulator-testing"></a>Testes do simulador

Ao testar no simulador, o **Inspetor de acessibilidade** está disponível para ajudar a verificar eventos e propriedades de acessibilidade estão configurados corretamente. Ativar o Inspetor do **as configurações** aplicativo navegando para **geral > Acessibilidade > Inspetor de acessibilidade**:

![](accessibility-images/settings-inspector-sml.png "Habilitar o Inspector de acessibilidade")

Uma vez habilitada, a janela Inspetor passa o mouse sobre a tela do iOS em todos os momentos.
Aqui está um exemplo da saída quando uma linha do modo de exibição de tabela é selecionada – Observe a **rótulo** contém uma frase que fornece o conteúdo da linha e também que ele é "concluído" (ie. a escala é visível):

![](accessibility-images/tableview-a11y-sml.png "Usando o Inspetor de acessibilidade")

Enquanto o Inspetor está visível, use o ícone "X" no canto superior esquerdo para temporariamente mostrar e ocultar a sobreposição e habilitar/desabilitar as configurações de acessibilidade.



## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS acessibilidade (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
