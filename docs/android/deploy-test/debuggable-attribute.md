---
title: Atributo depurável
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487965"
---
# <a name="debuggable-attribute"></a>Atributo depurável

Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desabilitado antes da publicação do aplicativo.

JDWP pode ser configurado pelo valor do atributo `android:debuggable` em um aplicativo Android. Escolha _uma_ das três maneiras a seguir para definir esse atributo no Xamarin. Android:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Crie ou abra `AndroidManifext.xml` arquivo e defina o atributo `android:debuggable`. Tome cuidado para não enviar sua compilação de versão com a depuração habilitada.

## <a name="add-an-application-class-attribute"></a>Adicionar um atributo de classe de aplicativo

Se seu aplicativo Xamarin. Android tiver uma classe com um atributo `[Application]`, atualize o atributo para `[Application(Debuggable = true)]`. Defina-o como `false` para desabilitar.

## <a name="add-an-assembly-attribute"></a>Adicionar um atributo de assembly

Se seu aplicativo Xamarin. Android ainda não tiver um atributo de classe `[Application]`, adicione um atributo de nível de assembly `[assembly: Application(Debuggable=true)]` em um arquivo c#. Defina-o como `false` para desabilitar.

## <a name="summary"></a>Resumo

Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se você adicionar um atributo de classe _e_ um atributo de assembly, haverá um erro de compilador:

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

Por padrão – se nem o `AndroidManifest.xml` nem o `ApplicationAttribute` estiver presente – o valor do atributo `android:debuggable` dependerá se os símbolos de depuração serão ou não gerados. Se os símbolos de depuração estiverem presentes, o Xamarin. Android definirá o atributo `android:debuggable` como `true` para você.

> [!WARNING]
> O valor do atributo `android:debuggable` não depende necessariamente da configuração da compilação. É possível que builds de versão definam o atributo `android:debuggable` para true. Se você usar um atributo para definir esse valor, poderá optar por encapsular o atributo em uma diretiva de compilador:
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>Links Relacionados

- [Aplicativos depuráveis no Android Market](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/)
