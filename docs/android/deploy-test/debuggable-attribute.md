---
title: Atributo depurável
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487965"
---
# <a name="debuggable-attribute"></a>Atributo depurável

Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desativado antes da publicação do aplicativo.

JDWP pode ser configurado pelo `android:debuggable` valor do atributo em um aplicativo Android. Escolha _uma_ das três maneiras a seguir para definir este atributo no Xamarin.Android:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Crie ou `AndroidManifext.xml` abra o `android:debuggable` arquivo e defina o atributo lá. Tome cuidado extra para não enviar sua compilação de liberação com a depuração ativada.

## <a name="add-an-application-class-attribute"></a>Adicionar um atributo de classe de aplicativo

Se o seu aplicativo Xamarin.Android `[Application]` tiver uma classe `[Application(Debuggable = true)]`com um atributo, atualize o atributo para . Configure-o para `false` desativar.

## <a name="add-an-assembly-attribute"></a>Adicionar um atributo de montagem

Se o aplicativo Xamarin.Android NÃO `[Application]` tiver um atributo de `[assembly: Application(Debuggable=true)]` classe, adicione um atributo de nível de montagem em um arquivo c#. Configure-o para `false` desativar.

## <a name="summary"></a>Resumo

Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se você adicionar um atributo de classe _e_ um atributo de montagem, haverá um erro do compilador:

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

Por padrão – `AndroidManifest.xml` se `ApplicationAttribute` nem o nem o `android:debuggable` presente está presente – o valor do atributo depende se símbolos de depuração são gerados ou não. Se os símbolos de depuração estiverem presentes, `android:debuggable` então `true` o Xamarin.Android definirá o atributo para você.

> [!WARNING]
> O valor `android:debuggable` do atributo NÃO depende necessariamente da configuração de compilação. É possível que builds de versão definam o atributo `android:debuggable` para true. Se você usar um atributo para definir esse valor, você pode optar por envolver o atributo em uma diretiva de compilador:
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>Links relacionados

- [Aplicativos depuráveis no Android Market](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/)
