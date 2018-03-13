---
title: Acessibilidade no macOS
description: "Este guia descreve os recursos para a criação de um aplicativo Xamarin.Mac acessível."
ms.topic: article
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4363258a9047ee4e2de4f53595a6eedc5dfe5861
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="accessibility-on-macos"></a>Acessibilidade no macOS

Esta página descreve como usar o macOS APIs de acessibilidade para compilar aplicativos de acordo com o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte o [acessibilidade Android](~/android/app-fundamentals/accessibility.md) e [iOS acessibilidade](~/ios/app-fundamentals/accessibility.md) páginas para outras APIs da plataforma.

Para entender como funcionam as APIs de acessibilidade no macOS (anteriormente chamado OS X), primeiro examine o [modelo de acessibilidade do sistema operacional X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Descrever os elementos de interface do usuário

AppKit usa o `NSAccessibility` protocolo para expor APIs que ajudam a tornar acessível a interface do usuário. Isso inclui um comportamento padrão que tenta definir valores significativos para as propriedades de acessibilidade, como definir um botão `AccessibilityLabel`. O rótulo é normalmente uma única palavra ou frase curta que descreve o controle ou o modo de exibição.

### <a name="storyboard-files"></a>Arquivos de storyboard

Xamarin.Mac usa o construtor de Interface do Xcode para editar arquivos de storyboard.
Informações de acessibilidade podem ser editadas no **Inspetor de identidade** quando um controle está selecionado na superfície de design (conforme mostrado na captura de tela abaixo):

[![A adição de acessibilidade no construtor de Interface do Xcode](accessibility-images/xcode.png "adicionando acessibilidade no construtor de Interface do Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Código

Xamarin.Mac não expõe atualmente como `AccessibilityLabel` setter.  Adicione o método auxiliar para definir o rótulo de acessibilidade:

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

O `AccessibilityHelp` é de propriedade para obter uma explicação do que o controle ou o modo de exibição faz e devem ser adicionados apenas quando o rótulo não pode fornecer informações suficientes. O texto de Ajuda ainda deve ser mantido tão curto quanto possível, por exemplo "exclui o documento".

Alguns elementos de interface do usuário não são relevantes para acesso acessível (como um rótulo ao lado de uma entrada que tem seu próprio rótulo de acessibilidade e ajuda).
Nesses casos, definir `AccessibilityElement = false` para que esses controles ou modos de exibição serão ignorados por leitores de tela ou outras ferramentas de acessibilidade.

Apple fornece [diretrizes de acessibilidade](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) que explica as práticas recomendadas para o texto de Ajuda e rótulos de acessibilidade.

## <a name="custom-controls"></a>Controles personalizados

Consulte da Apple [diretrizes para controles personalizados acessíveis](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) para obter detalhes sobre as etapas adicionais necessárias.

## <a name="testing-accessibility"></a>Teste de acessibilidade

macOS fornece um **Inspetor de acessibilidade** que ajuda a testar a funcionalidade de acessibilidade. O Inspetor está incluído no Xcode.

Na primeira vez que ele é iniciado, o **Inspetor de acessibilidade** precisará de permissão para controlar o computador por meio de acessibilidade:

![Inspetor de acessibilidade solicitando permissão para executar](accessibility-images/accessibility-inspector-1.png "Inspetor de acessibilidade solicitando permissão para executar")

Desbloquear a tela de configurações (se necessário, no canto inferior esquerdo) e a escala de **Inspetor de acessibilidade**:

![Tela de configurações para habilitar o Inspetor de acessibilidade](accessibility-images/accessibility-inspector-2.png "tela de configurações para habilitar o Inspetor de acessibilidade")

Uma vez habilitada, o Inspetor de aparece como uma janela flutuante que pode ser movida pela tela. Captura de tela abaixo mostra o Inspetor de execução ao lado de uma amostra de aplicativo do Mac. Como o cursor é movido sobre a janela, o Inspetor exibe todas as propriedades acessíveis de cada controle:

[![Exemplo de execução do Inspetor de acessibilidade](accessibility-images/accessibility-example.png "em execução do Inspetor de exemplo de acessibilidade")](accessibility-images/accessibility-example-large.png#lightbox)

Para obter mais informações, leia o [testes de acessibilidade para o guia de OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [Acessibilidade do Mac](https://www.apple.com/accessibility/mac/)
