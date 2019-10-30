---
title: Atributo depurável
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 55de652363cb761e730d2d92f66a14a941899ca2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028107"
---
# <a name="debuggable-attribute"></a>Atributo depurável

Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desabilitado antes do aplicativo ser publicado.

O JDWP pode ser o valor do atributo `android:debuggable` em um aplicativo Android. O Xamarin.Android fornece as seguintes maneiras de definir esse atributo:

1. Criar um arquivo `AndroidManifext.xml` e configurando o atributo `android:debuggable` nele.
2. Incluir o `ApplicationAttribute` em um arquivo `.CS`, da seguinte forma: `[assembly: Application(Debuggable=false)]`.

Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se `AndroidManifest.xml` nem `ApplicationAttribute` estiverem presentes, o valor padrão do atributo `android:debuggable` dependerá da geração ou não de símbolos de depuração. Se os símbolos de depuração estiverem presentes, o Xamarin.Android definirá o atributo `android:debuggable` para `true`.

Observe que o valor do atributo `android:debuggable` NÃO depende necessariamente da configuração de build. É possível que builds de versão definam o atributo `android:debuggable` para true.

## <a name="related-links"></a>Links relacionados

- [Aplicativos depuráveis no Android Market](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/)
