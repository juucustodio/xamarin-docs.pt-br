---
title: Acessibilidade no macOS
description: Este documento descreve como trabalhar com recursos de acessibilidade do macOS em um aplicativo Xamarin. Mac. Ele aborda a descrição de elementos de interface do usuário em storyboards e código, controles personalizados e a acessibilidade de teste.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: e92887dc54acc33e6d433aafe39ae7df9ea8ac71
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291658"
---
# <a name="accessibility-on-macos"></a>Acessibilidade no macOS

Esta página descreve como usar as APIs de acessibilidade do macOS para compilar aplicativos de acordo com a [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md).
Consulte as páginas de acessibilidade do [Android](~/android/app-fundamentals/accessibility.md) e acessibilidade do [Ios](~/ios/app-fundamentals/accessibility.md) para outras APIs de plataforma.

Para entender como as APIs de acessibilidade funcionam no macOS (anteriormente chamado de OS X), primeiro examine o [modelo de acessibilidade os x](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Descrevendo elementos da interface do usuário

O AppKit usa `NSAccessibility` o protocolo para expor APIs que ajudam a tornar a interface do usuário acessível. Isso inclui um comportamento padrão que tenta definir valores significativos para propriedades de acessibilidade, como a definição de um botão `AccessibilityLabel`. Normalmente, o rótulo é uma única palavra ou frase curta que descreve o controle ou a exibição.

### <a name="storyboard-files"></a>Arquivos de storyboard

O Xamarin. Mac usa o Interface Builder do Xcode para editar arquivos de storyboard.
As informações de acessibilidade podem ser editadas no **Inspetor de identidade** quando um controle é selecionado na superfície de design (conforme mostrado na captura de tela abaixo):

[![Adicionando acessibilidade no interface Builder do Xcode](accessibility-images/xcode.png "Adicionando acessibilidade no interface Builder do Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Código

O Xamarin. Mac não é exposto atualmente `AccessibilityLabel` como setter.  Adicione o seguinte método auxiliar para definir o rótulo de acessibilidade:

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

Esse método pode então ser usado no código, conforme mostrado:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

A `AccessibilityHelp` propriedade é para uma explicação do que o controle ou exibição faz e deve ser adicionada somente quando o rótulo não fornecer informações suficientes. O texto de ajuda ainda deve ser mantido o mais curto possível, por exemplo, "excluir o documento".

Alguns elementos da interface do usuário não são relevantes para acesso acessível (como um rótulo ao lado de uma entrada que tem seu próprio rótulo de acessibilidade e ajuda).
Nesses casos, defina `AccessibilityElement = false` para que esses controles ou exibições sejam ignorados por leitores de tela ou outras ferramentas de acessibilidade.

A Apple fornece [diretrizes de acessibilidade](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) que explicam as práticas recomendadas para rótulos de acessibilidade e texto de ajuda.

## <a name="custom-controls"></a>Controles personalizados

Consulte as diretrizes da Apple [para obter controles personalizados acessíveis](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) para obter detalhes sobre as etapas adicionais necessárias.

## <a name="testing-accessibility"></a>Testando a acessibilidade

o macOS fornece um **Inspetor de acessibilidade** que ajuda a testar a funcionalidade de acessibilidade. O inspetor está incluído no Xcode.

Na primeira vez que for iniciado, o **Inspetor de acessibilidade** exigirá permissão para controlar o computador por meio de acessibilidade:

![Inspetor de acessibilidade solicitando permissão para executar](accessibility-images/accessibility-inspector-1.png "Inspetor de acessibilidade solicitando permissão para executar")

Desbloqueie a tela de configurações (se necessário, na parte inferior esquerda) e marque o **Inspetor de acessibilidade**:

![Tela de configurações para habilitar o Inspetor de acessibilidade](accessibility-images/accessibility-inspector-2.png "Tela de configurações para habilitar o Inspetor de acessibilidade")

Uma vez habilitado, o Inspetor aparece como uma janela flutuante que pode ser movida pela tela. A captura de tela abaixo mostra o Inspetor em execução ao lado de um aplicativo Mac de exemplo. À medida que o cursor é movido sobre a janela, o Inspetor exibe todas as propriedades acessíveis de cada controle:

[![Exemplo de Inspetor de acessibilidade em execução](accessibility-images/accessibility-example.png "Exemplo de Inspetor de acessibilidade em execução")](accessibility-images/accessibility-example-large.png#lightbox)

Para obter mais informações, leia o [Guia de acessibilidade de teste para os X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Links relacionados

- [Acessibilidade entre plataformas](~/cross-platform/app-fundamentals/accessibility.md)
- [Acessibilidade do Mac](https://www.apple.com/accessibility/mac/)
