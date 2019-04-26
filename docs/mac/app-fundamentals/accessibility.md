---
title: Acessibilidade no macOS
description: Este documento descreve como trabalhar com recursos de acessibilidade do macOS em um aplicativo xamarin. Mac. Ele discute que descreve elementos de interface do usuário no código e storyboards, controles personalizados e teste de acessibilidade.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fdda52309ffdb0d32cc42a4dff052cd9050b1e4f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378425"
---
# <a name="accessibility-on-macos"></a>Acessibilidade no macOS

Esta página descreve como usar o APIs de acessibilidade do macOS para criar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte a [Android acessibilidade](~/android/app-fundamentals/accessibility.md) e [acessibilidade iOS](~/ios/app-fundamentals/accessibility.md) páginas para outras APIs da plataforma.

Para entender como funcionam as APIs de acessibilidade no macOS (anteriormente chamado de OS X), primeiro examine os [modelo de acessibilidade dos X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Que descreve os elementos de interface do usuário

AppKit usa o `NSAccessibility` protocolo para expor APIs que ajudam a tornar a interface do usuário acessível. Isso inclui um comportamento padrão que tenta definir valores significativos para propriedades de acessibilidade, como a configuração de um botão `AccessibilityLabel`. O rótulo é normalmente uma única palavra ou frase curta que descreve o controle ou o modo de exibição.

### <a name="storyboard-files"></a>Arquivos de storyboard

Xamarin. Mac usa o Interface Builder do Xcode para editar arquivos de storyboard.
Informações de acessibilidade podem ser editadas na **Inspetor de identidade** quando um controle for selecionado na superfície de design (como mostrado na captura de tela abaixo):

[![Adição de acessibilidade no Interface Builder do Xcode](accessibility-images/xcode.png "adicionando acessibilidade no Interface Builder do Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Código

Xamarin. Mac no momento não expor como `AccessibilityLabel` setter.  Adicione o método auxiliar a seguir para definir o rótulo de acessibilidade:

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Esse método, em seguida, pode ser usado no código, conforme mostrado:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

O `AccessibilityHelp` é de propriedade para obter uma explicação do que o controle ou o modo de exibição faz e só deve ser adicionada quando o rótulo não pode fornecer informações suficientes. O texto de Ajuda ainda deve ser mantido tão curto quanto possível, por exemplo "exclui o documento".

Alguns elementos de interface do usuário não são relevantes para acesso acessível (por exemplo, um rótulo ao lado de uma entrada que tem seu próprio rótulo de acessibilidade e ajuda).
Nesses casos, definir `AccessibilityElement = false` , de modo que esses controles ou modos de exibição serão ignorados por leitores de tela ou outras ferramentas de acessibilidade.

A Apple fornece [diretrizes de acessibilidade](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) que explica as práticas recomendadas para o texto de Ajuda e rótulos de acessibilidade.

## <a name="custom-controls"></a>Controles personalizados

Consulte da Apple [diretrizes para controles personalizados acessíveis](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) para obter detalhes sobre as etapas adicionais necessárias.

## <a name="testing-accessibility"></a>Teste de acessibilidade

macOS fornece um **Inspetor de acessibilidade** que ajuda a testar a funcionalidade de acessibilidade. O Inspetor está incluído com o Xcode.

Na primeira vez que ele é iniciado, o **Inspetor de acessibilidade** exigirá permissão para controlar o computador por meio de acessibilidade:

![Solicitando permissão para executar o Inspetor de acessibilidade](accessibility-images/accessibility-inspector-1.png "solicitando permissão para executar o Inspetor de acessibilidade")

Desbloquear a tela de configurações (se necessário, no canto inferior esquerdo) e a escala a **Inspetor de acessibilidade**:

![Tela de configurações para habilitar o Inspector de acessibilidade](accessibility-images/accessibility-inspector-2.png "tela de configurações para habilitar o Inspector de acessibilidade")

Uma vez habilitada, o Inspetor de é exibido como uma janela flutuante que pode ser movida pela tela. Captura de tela abaixo mostra o Inspetor de execução ao lado de um aplicativo de exemplo do Mac. À medida que o cursor é movido sobre a janela, o Inspetor exibe todas as propriedades acessíveis de cada controle:

[![Exemplo de execução do Inspetor de acessibilidade](accessibility-images/accessibility-example.png "em execução do Inspetor de exemplo de acessibilidade")](accessibility-images/accessibility-example-large.png#lightbox)

Para obter mais informações, leia as [teste de acessibilidade para o guia dos X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [Acessibilidade do Mac](https://www.apple.com/accessibility/mac/)
