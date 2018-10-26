---
title: Atributo depurável
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 66bbc7015daaba04f8431b31f639c9173484c790
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108659"
---
# <a name="debuggable-attribute"></a>Atributo depurável



Para possibilitar a depuração, o Android é compatível com o JDWP (protocolo de transmissão de depuração do Java). Essa é uma tecnologia que permite que ferramentas como ADB se comuniquem com uma JVM. Embora o JDWP seja importante durante o desenvolvimento, ele deve ser desabilitado antes do aplicativo ser publicado.

O JDWP pode ser o valor do atributo `android:debuggable` em um aplicativo Android. O Xamarin.Android fornece as seguintes maneiras de definir esse atributo:

1.  Criar um arquivo `AndroidManifext.xml` e configurando o atributo `android:debuggable` nele.
2.  Incluir o `ApplicationAttribute` em um arquivo `.CS`, da seguinte forma: `[assembly: Application(Debuggable=false)]`.


Se `AndroidManifest.xml` e `ApplicationAttribute` estiverem presentes, o conteúdo de `AndroidManifest.xml` tem prioridade sobre o que é especificado pelo `ApplicationAttribute`.

Se `AndroidManifest.xml` nem `ApplicationAttribute` estiverem presentes, o valor padrão do atributo `android:debuggable` dependerá da geração ou não de símbolos de depuração. Se os símbolos de depuração estiverem presentes, o Xamarin.Android definirá o atributo `android:debuggable` para `true`.

Observe que o valor do atributo `android:debuggable` NÃO depende necessariamente da configuração de build. É possível que builds de versão definam o atributo `android:debuggable` para true.


## <a name="related-links"></a>Links relacionados

- [Aplicativos depuráveis no Android Market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
