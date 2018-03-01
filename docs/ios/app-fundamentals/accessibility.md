---
title: Acessibilidade no iOS
ms.topic: article
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/18/2016
ms.openlocfilehash: 28701daca18852be93724ddfe444e1e620788619
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="accessibility-on-ios"></a>Acessibilidade no iOS

Esta página descreve como usar as APIs de acessibilidade do iOS para compilar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte o [acessibilidade Android](~/android/app-fundamentals/accessibility.md) e [acessibilidade OS X](~/mac/app-fundamentals/accessibility.md) páginas para outras APIs da plataforma.

## <a name="describing-ui-elements"></a>Descrever os elementos de interface do usuário

iOS fornece o `AccessibilityLabel` e `AccessibilityHint` leitor para disponibilizar mais os controles de tela de propriedades para os desenvolvedores adicionar texto descritivo que pode ser usado pela narração. Controles também podem ser marcados com um ou mais características que fornecem contexto adicional nos modos acessíveis.

Alguns controles talvez não precise estar acessível (por exemplo, um rótulo em um entrada de texto ou uma imagem que é puramente decorativa) – o `IsAccessibilityElement` é fornecido para desabilitar a acessibilidade nesses casos.

**Designer de interface do usuário**

O **propriedades de preenchimento** contém uma seção de acessibilidade que permite que essas configurações sejam editados quando um controle está selecionado no Designer de interface do usuário do iOS:

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

O valor de `AccessibilityIdentifier` nunca é falada ou exibido ao usuário.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

O `UIAccessibility.PostNotification` método permite que os eventos a serem gerados para o usuário fora de interação direta (por exemplo, quando eles interagem com um controle específico).

### <a name="announcement"></a>Anúncio

Um anúncio pode ser enviado de código para informar ao usuário que algum estado mudou (como uma operação em segundo plano foi concluída). Isso pode ser acompanhado por uma indicação visual na interface do usuário:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

O `LayoutChanged` anúncio é usado quando o layout da tela:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>Acessibilidade e localização

Como o rótulo e dica podem ser localizados apenas as propriedades de acessibilidade como outro texto na interface do usuário.

**MainStoryboard.strings**

Se a interface do usuário é apresentada em um storyboard, você pode fornecer traduções para as propriedades de acessibilidade da mesma maneira como outras propriedades. No exemplo a seguir, uma `UITextField` tem um **identificação da localização** de `Pqa-aa-ury` duas propriedades de acessibilidade que está sendo definidas em espanhol e:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Este arquivo deve ser colocado no **es.lproj** diretório de conteúdo espanhol.

**Localizable.strings**

Como alternativa, as traduções podem ser adicionadas para o **Localizable.strings** arquivo (por exemplo, o diretório de conteúdo localizado **es.lproj** para espanhol):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Essas conversões podem ser usados no c# via o `LocalizedString` método:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Consulte o [guia de localização do iOS](~/ios/app-fundamentals/localization/index.md) para obter mais detalhes sobre a localização de conteúdo.

<a name="testing" />

## <a name="testing-accessibility"></a>Teste de acessibilidade

Leitura está habilitada no **configurações** aplicativo navegando até **geral > Acessibilidade > VoiceOver**:

![](accessibility-images/settings-sml.png "Configuração da taxa de fala")

O **acessibilidade** tela também fornece as configurações de zoom, tamanho do texto, opções de cor e contraste, as configurações de voz e outras opções de configuração.

Siga estas [instruções VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) para testar a acessibilidade em dispositivos iOS.


## <a name="simulator-testing"></a>Teste do simulador

Ao testar no simulador, o **Inspetor de acessibilidade** está disponível para ajudar a verificar as propriedades de acessibilidade e eventos estão configurados corretamente. Ativar o Inspetor no **configurações** aplicativo navegando até **geral > Acessibilidade > Inspetor de acessibilidade**:

![](accessibility-images/settings-inspector-sml.png "Habilitar o Inspetor de acessibilidade")

Uma vez habilitada, a janela Inspetor focaliza a tela de iOS em todos os momentos.
Aqui está um exemplo da saída quando uma linha de exibição de tabela é selecionada, observe o **rótulo** contém uma frase que fornece o conteúdo da linha e também que ele é "done" (ie. a escala é visível):

![](accessibility-images/tableview-a11y-sml.png "Usando o Inspetor de acessibilidade")

Enquanto o Inspetor estiver visível, use o ícone "X" no canto superior esquerdo para temporariamente mostrar e ocultar a sobreposição e habilitar/desabilitar as configurações de acessibilidade.



## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS acessibilidade (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
