---
title: Acessibilidade no iOS
description: Este documento descreve a acessibilidade no iOS, discutindo várias propriedades e recursos que podem ser usados para tornar seu aplicativo utilizável com o máximo de usuários possível.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/18/2016
ms.openlocfilehash: 31cae6d6770b4c8fc4ff722e67f4ddce8ffdd7c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011299"
---
# <a name="accessibility-on-ios"></a>Acessibilidade no iOS

Esta página descreve como usar as APIs de acessibilidade do iOS para compilar aplicativos de acordo com a [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte as páginas de acessibilidade do [Android e acessibilidade](~/android/app-fundamentals/accessibility.md) do [os X](~/mac/app-fundamentals/accessibility.md) para outras APIs de plataforma.

## <a name="describing-ui-elements"></a>Descrevendo elementos da interface do usuário

o iOS fornece as propriedades `AccessibilityLabel` e `AccessibilityHint` para que os desenvolvedores adicionem texto descritivo que pode ser usado pelo leitor de tela do VoiceOver para tornar os controles mais acessíveis. Os controles também podem ser marcados com uma ou mais características que fornecem contexto adicional em modos acessíveis.

Alguns controles podem não precisar ser acessíveis (por exemplo, um rótulo em uma entrada de texto ou uma imagem puramente decorativa) – a `IsAccessibilityElement` é fornecida para desabilitar a acessibilidade nesses casos.

**Designer de interface do usuário**

O **painel de propriedades** contém uma seção de acessibilidade que permite que essas configurações sejam editadas quando um controle é selecionado no designer de interface do usuário do IOS:

![](accessibility-images/ios-designer-sml.png "Accessibility Settings")

**C#**

Essas propriedades também podem ser definidas diretamente no código:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>O que é o AccessibilityIdentifier?

O `AccessibilityIdentifier` é usado para definir uma chave exclusiva que pode ser usada para fazer referência a elementos da interface do usuário por meio da API automação da IU.

O valor de `AccessibilityIdentifier` nunca é falado ou exibido para o usuário.

<a name="postnotification" />

## <a name="postnotification"></a>Notificação por

O método `UIAccessibility.PostNotification` permite que os eventos sejam gerados para o usuário fora da interação direta (por exemplo, quando eles interagem com um controle específico).

### <a name="announcement"></a>Nascimento

Um comunicado pode ser enviado do código para informar ao usuário que algum estado foi alterado (como uma operação em segundo plano concluída). Isso pode ser acompanhado por uma indicação visual na interface do usuário:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>Layoutchanged

O anúncio de `LayoutChanged` é usado quando o layout da tela:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```

## <a name="accessibility-and-localization"></a>Acessibilidade e localização

As propriedades de acessibilidade, como o rótulo e a dica, podem ser localizadas da mesma forma que outro texto na interface do usuário.

**MainStoryboard. Strings**

Se a interface do usuário for disposta em um storyboard, você poderá fornecer traduções para propriedades de acessibilidade da mesma maneira que outras propriedades. No exemplo a seguir, um `UITextField` tem uma **ID de localização** de `Pqa-aa-ury` e duas propriedades de acessibilidade sendo definidas em espanhol:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Esse arquivo seria colocado no diretório **es. lproj** para conteúdo espanhol.

**As cadeias de caracteres localizáveis**

Como alternativa, as traduções podem ser adicionadas ao arquivo **localizável. Strings** no diretório de conteúdo localizado (por exemplo, **es. lproj** para espanhol):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Essas traduções podem ser usadas C# no por meio do método `LocalizedString`:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Consulte o [Guia de localização do IOS](~/ios/app-fundamentals/localization/index.md) para obter mais detalhes sobre como localizar conteúdo.

<a name="testing" />

## <a name="testing-accessibility"></a>Testando a acessibilidade

O VoiceOver está habilitado no aplicativo **configurações** navegando para **geral > acessibilidade > VoiceOver**:

![](accessibility-images/settings-sml.png "Setting the speaking rate")

A tela de **acessibilidade** também fornece configurações de zoom, tamanho do texto, cor & opções de contraste, configurações de fala e outras opções de configuração.

Siga estas [instruções do VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) para testar a acessibilidade em dispositivos IOS.

## <a name="simulator-testing"></a>Testes de simulador

Ao testar no simulador, o **Inspetor de acessibilidade** está disponível para ajudar a verificar se as propriedades de acessibilidade e os eventos estão configurados corretamente. Ative o Inspetor no aplicativo **configurações** navegando para **geral > acessibilidade > Inspetor de acessibilidade**:

![](accessibility-images/settings-inspector-sml.png "Enable Accessibility Inspector")

Uma vez habilitada, a janela do Inspetor focaliza a tela do iOS em todos os momentos.
Aqui está um exemplo da saída quando uma linha de exibição de tabela é selecionada – Observe que o **rótulo** contém uma sentença que fornece o conteúdo da linha e também que é "Done" (ou seja, o tique é visível):

![](accessibility-images/tableview-a11y-sml.png "Using Accessibility Inspector")

Enquanto o Inspetor estiver visível, use o ícone "X" na parte superior esquerda para mostrar temporariamente e ocultar a sobreposição e habilitar/desabilitar as configurações de acessibilidade.

## <a name="related-links"></a>Links relacionados

- [Acessibilidade multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Acessibilidade do iOS (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [VoiceOver do iOS](https://www.apple.com/accessibility/ios/voiceover/)
